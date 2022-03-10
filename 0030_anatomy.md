---
label: Anatomie d'un projet
route: /anatomy
---

Par défault, un projet créé avec le client `foundation` utilisera le template projet que nous recommandons.
Ce template est un projet Gradle avec trois modules : `api`, `core` et `service`.  


![Anamtomie d'un projet Foundation](/static/img/project_anatomy.png)

## Module API

Contient la description, avec un vocabulatire fonctionnel, de ce que l'application **doit** implémenter come **usecases** et **ressources**.

![Module API](/static/img/project_api_module.png)

Dans les packages qui sont proposés, vous retrouvez:

### Package api.schema
Ce package contient le modèle de données (POJO) manipulés et produits par vos usecase

### Package api.usecases
Ce package contient les interfaces décrivant les fonctionnalités du projet. Vous retrouvez dans ce dossier aussi la classe `Input` associé à chaque usecase. Même s'il s'agit d'un POJO, pour des besoins de lisibilité, nous avons fait le choit de mettre chaque classe input à côté du Usecase associé.

```java
/* api/usecases/Echo.java */

import dev.soffa.foundation.core.UseCase;
import foundation.app.api.schema.Message;

public interface Echo extends UseCase<EchoInput, Message> { }
```

La définition d'un UseCase est une simple interface qui étend `dev.soffa.foundation.core.UseCase`. 
En ouvrant l'interface, on peut donc avoir une idée générale de que cette opération est censée faire, quel est l'input et quel est l'output.

Il est donc recommandé de toujours choisir une terminologie qui la plus fonctionnielle possible: <a href="https://www.martinfowler.com/bliki/UbiquitousLanguage.html" target="_blank"><em>UbiquutUbiquitous Language</em></a>.


### Package api

Ce package contient les interfaces des ressources que le projet expose (en REST, GraphQL, etc).
Une ressource est une interface qui expose plusieurs opérations. Foundation inclue plusieurs dépendances par défaut, dont OpenAPI pour la documentation des APIs.

![](/static//img/project_api_resource.png)

Une opération dans une ressource attendra toujours le même `Input` que le UseCase auquel elle est associée.
Dans le cas ci-après, l'opération `Echo.echo` est associé au UseCase `Echo`.

### Importance du module API

Le module API permet de décrire le fonctionnement de votre service mais aussi de servir d'interface pour les clients. En effet, le module `api` pouvant être publié séparement dans un serveur nexus, 
peut être rajouté comme dépendance dans un autre service dans lequel le code suivant peut etre utilisé :

```java
/* Logique présent dans un service-a souhaitant consommer une ressource du service-b */
EchoResource client = RestClient.newInstance(EchoResource.class, "https://service-b");
Message response = client.echo(new EchoInput("Hello"), new Context());
```

Il n'y a donc aucune génération de client à prévoir lorsque votre ressource doit être consommer depuis un code Java/Kotlin.


## Module Core

Contient l'implémentation des **usecases** (qui contiennent les règles de gestion). Cette implémentation peut nécessister l'accès à des ressources externes au système. L'interface de cette ressource externe est alors créée dans ce module et l'implémentation déléguée à la couche service

## Module Service

Contient le framework Springboot et la configuration nécessaire pour accéder aux ressources externes (en entrée et en sortie)



