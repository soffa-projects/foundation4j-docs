---
order: 100
title: Persistence
---

# Persistence de données

Pour rappel, Foundation est construit par dessus SpringBoot. 
Tout ce que vous avez l'habitude de faire avec Spring Boot en terme de persistence de données reste valide.

Toutefois, la philosophie derrière Foundation est d'offrir une abstraction aux développeurs.

## Limitations Spring Boot Repository

Se connecter à une base de données et faire de la gestion de données est relativement quelque chose de simple
à mettre en place avec SpringBoot.

```properties
# PostgreSQL
spring.datasource.url = jdbc:postgresql://localhost:5432/datasource
spring.datasource.username = postgres
spring.datasource.password = postgres
spring.datasource.driver-class-name = com.postgresql.jdbc.Driver
```

```java
@Repository
public interface ProductRepository extends JpaRepository<Product, Long> {
    Product findByName(String productName);
}
```

Mais voilà, lorsque le besoin se présente de :
- Gérer plusieurs bases de données
- Créer une application multi-tenante
- Faire de la gestion avancée de sharding
- Séparer les opérations read/write

la configuration SpringBoot + Hibernate n'est pas forcément la façon la plus flexible de faire les choses.

## Implémentation Foundation

Foundation utilise [https://jdbi.org](jdbi.org) pour proposer aux développeurs un mécanisme léger mais riche et flexible
pour gérer l'accès aux données.

```kotlin
# Dépendance
implementation("dev.soffa.foundation:foundation-starter-data")
```

### Configuration

La couche persistence propose une structure qui vous permet (par défaut) de déclarer plusieurs chaînes de connexion. 


```yaml application-default.yml
app.db.datasources:
  default:
    url: h2://mem/default # pg://postgres:postgres@localhost/foundation
    migration: application # Chaque datasource peut pointer sur une fichier de migration spécifiques

```

Parce qu'il est important de toujours gérer vos données via des fichiers de migration, Foundation supporte les migrations
au format [Liquibase](https://www.liquibase.org/). 

!!!important
Foundation n'utilise pas Hibernate. Il n'est donc pas possible d'utiliser la fonctionnalité (non recommandée pour de la production)
de génération automatique du schéma de votre base de données.
!!!


### Foundation Repository

Foundation offre une interface `EntityRepository<T>` pour vous permettre de faire les différentes opérations CRUD.

```java
@Repository(collection = "users")
public interface UserRepository extends EntityRepository<User> {
}
```

Cette interface expose les méthodes suivantes :


#### `count()`

```java
@Inject
private  UserRepository users;

users.count();
users.count(ImmutableMap.of("role", "admin"));
users.count(Criteria.of("role = :role", ImmutableMap.of("role", "admin")));
```


#### `exists()` 

```java
boolean hasAdmin = users.exists(ImmutableMap.of("username", "admin@bantu.dev"));
hasAdmin = users.exists(Criteria.of("username = :arg", ImmutableMap.of("arg", "admin@bantu.dev")));
```


#### `find()`

```java
List<User> userList = users.findAll();
userList = users.find(ImmutableMap.of("role", "admin"));
userList = users.find(Criteria.of("role = :role", ImmutableMap.of("role", "admin")));

```

#### `findById()`

```java
Optional<User> user = users.findById("arn-xo-1217218728178");

```


#### `get()` 

```java
Optional<User> user = users.get(ImmutableMap.of("username", "admin@bantu.dev"));
user = users.get(Criteria.of("username = :arg", ImmutableMap.of("arg", "admin@bantu.dev")));

```

#### `insert()` 

```java
User user = users.insert(new User(...));
```

#### `update()` 

```java

User user = /* ... */;
users.update(user);

```

#### `delete()` 

```java
long deleted = users.delete(ImmutableMap.of("status", "inactive"));
deleted = users.delete(
    Criteria.of(
        "status = :arg0 OR status = :arg1", 
        ImmutableMap.of("arg0", "INACTIVE", "arg1", "DELETED")
    )
);
```