---
label: Module API
order: 99
---


La définition de l'API (interfaces permettant d'exposer au monde extérieur les fonctionnalités du service)
se fait dans le module `app-api`.
L'objecti est de décrire, à travers un vocabulatire fonctionnel, ce que l'application **doit** implémenter comme **operations** et **ressources**.

Dans les packages qui sont proposés, vous retrouvez:

### api.schema
Ce package contient le modèle de données (POJO) manipulés et produits par les opérations.

### api.operation
Ce package contient les interfaces décrivant les fonctionnalités du projet. Chaque opération utilise un verbe en anglais pour clairement décrire 
la responsabilité.

En terme de bonnes pratiques, nous recommandons ce qui suit:

- Un verbe commençant par `Get` indique qu'il s'agit d'une opération de lecture (Query)
- Un verbe commençant par `On` indique qu'il s'git d'un événement (Event)
- Toutes les autres opérations sont considérées comme des opérations d'écriture (Command)

```java Echo.java
package foundation.app.api.operations;

import dev.soffa.foundation.core.Operation;
import foundation.app.api.schema.EchoInput;
import foundation.app.api.schema.Message;

public interface Echo extends Operation<EchoInput, Message> {}

```

La définition d'une _Operation_ est une simple interface qui étend `dev.soffa.foundation.core.Operation`. 
En ouvrant l'interface, on peut donc avoir une idée générale de que cette opération est censée faire, quel est l'input et quel est l'output.

Il est donc recommandé de toujours choisir une terminologie fonctionnelle simple à comprendre : <a href="https://www.martinfowler.com/bliki/UbiquitousLanguage.html" target="_blank"><em>Ubiquitous Language</em></a>.


### api.resources

Ce package contient les interfaces des ressources que le projet expose (en REST, GraphQL, etc).
Une ressource est une interface qui expose plusieurs opérations. Foundation inclut plusieurs dépendances par défaut, dont OpenAPI pour la documentation des APIs.

```java EchoResource.java
@Tags(
    @Tag(name = "Echo", description = "All things echo messaging")
)
@RestController // Cette interface sera implémentée automatiquement lorsque cette annotation est présente + Resource
public interface EchoResource extends Resource {

    /**
     * Commentaire développeur pour la méthode echo
     *
     * @param input   Données utilisateur
     * @param context Injectée automatiquement avec les informations de la requête en cours (n'apparaît pas dans la documentation OpenAPI)
     */
    @Operation(
        method = "POST",
        summary = "Echo the input message"
    )
    @PostMapping("echo")
    default Message echo(@Valid @RequestBody EchoInput input, Context context) {
        return invoke(Echo.class, input, context);
    }

}

```

La méthode `invoke` déclarée dans l'inteface `Resource` permet au développeur d'invoquer l'implémentation de l'opération définie par l'interface passée en premier.

!!!
Même si vous constatez des annotations SpringBoot à ce niveau, il ne s'agit que d'annotations. Les fichier correspodants ont été copier depuis le projet officiel Spring
et coller dans `foundation-api` pour ne pas avoir à inclure toute la dépendance Spring dans ce module API. 
L'interprétation de ces interfaces n'est faite que dans la partie `app-service`.
!!!



**Importance du module API

Le module API permet de décrire le fonctionnement de votre service mais aussi de servir d'interface pour les clients. En effet, le module `api` pouvant être publié séparement dans un serveur nexus, 
peut être rajouté comme dépendance dans un autre service dans lequel le code suivant peut etre utilisé :

```java
/* Logique présent dans un service-a souhaitant consommer une ressource du service-b */
EchoResource client = RestClient.newInstance(EchoResource.class, "https://service-b");
Message response = client.echo(new EchoInput("Hello"), new Context());
```

Il n'y a donc aucune génération de client à prévoir lorsque votre ressource doit être consommer depuis un code Java/Kotlin.


## Exposition


### Exposition Rest

Par défault toutes vos ressources sont exposées en Rest. C'est une valeur sûre de toujours commencer par ce type d'exposition.
Les différents annotations importées depuis SpringBoot `@RestController`, `RequestMapping` (...) permettent la création de ressources REST.
Il n'y rien de particulier rajouté par Foundation sur cette partie.

#### Hateos

L'annotation suivante:

```kotlin
import dev.soffa.foundation.annotation.Hateos

@Hateos
data class AccountList(
    val accounts: List<Account>
)
```

vous permet d'activer la fonctionnalité [HATEOS](https://restfulapi.net/hateoas) pour les opérations de type `GET` retournant la classe ciblée.

> Cette fonctionnalité est en développement actif, l'API est encore sujet à des changements.

### Exposition GraphQL (WIP)

Le support GraphQL est toujours en construction, et prévue pour une release ultérieure.
Pour cette implémentation nous avons retenu [Netflix DGS](https://netflix.github.io/dgs).
Cette section sera mise à jour une fois cette action finalisée.



## Documentation des ressources

### Redoc

En supposant que votre projet soit configué sur le port 8080, lorsque vous démarrez l'application, la age d'accueil
vous afficher la présentation de votre API avec [Redoc](https://github.com/Redocly/redoc)

![http://localhost:8080](/static/img/app_redoc.png)

#### Configuration

```yaml
app.redoc.enabled: true # ou false
```

### OpenAPI

![http://localhost:8080/swagger-ui/index.html](/static/img/app_openapi.png)

#### Configuration

```yaml
app.swagger-ui.enabled: true  # ou false
app.openapi.access: permitAll # ou authenticated
```

Pour personnaliser les informations de votre API, nous recommandons de créer un fichier `openapi.yml` et de l'importer
dans votre fichier `application.yml` comme suit:

```yaml application.yaml
app.name: foundation-starter

spring.config.import: openapi.yml
```

```yaml openapi.yml

app.openapi:
  # Information de base de votre API
  info:
    title: Demo API
    version: 1.0.0
    description: Foundation demo API # Supporte le format Markdown
    contact:
      name: Name of your team
      url: http://noop
      email: noreply@email.com
  
  # Décommentez cette partie pour spécifiez les méthodes d'authentification supportées par votre service
  #security:
  #  bearerAuth: true
  #  basicAuth: true
  #  oauth2:
  #    url: ${OAUTH2_SERVER:http://localhost:9000}
  #    scopes: ${OAUTH2_SCOPES:openid}
  #    authorizationCode: true
  
  # Décommentez cette partie pour déclarer des headers sur toutes vos ressources
  #parameters:
  #  - name: X-TenantId
  #    in: header
  #    description: Description of the header parameter # Markdown
  #    values: [ T1, T2n ]


