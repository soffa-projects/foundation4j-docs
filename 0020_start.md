---
label: Bien démarrer
order: 30
route: /start
---

!!!
Nous recommandons l'utilisation de Foundation si vous souhaitez construire un projet entreprise long terme (de taille moyenne ou grande) avec
une importante dans la maintenabilité et la qualité.
!!!

Pour démarrer un projet avec Foundation4j, nous recommandons de télécharger le repository [foundation-java-starter](https://github.com/soffalabs/foundation-java-starter),
puis suivre les 2 étapes suivantes:

1. Changer le nom du projet

```groovy settings.gradle.kts
rootProject.name = "foundation-starter" //TODO: Change this to your project name

// Cette partie ne devrait pas être modifiée si vous restez dans la structure
// par défaut proposée.
listOf("api", "service").forEach {
    include(":app-$it")
    project(":app-$it").name = rootProject.name + "-$it"
}
```

2. Personnaliser le package, par défault à `foundation.app`

Voilà, vous pouvez commencer à travailler !


## Anatomie d'un projet


Par défault, un projet créé avec le client `foundation` utilisera le template projet que nous recommandons.
Ce template est un projet Gradle avec deux modules : `api` et `service`.  


![Anatomie d'un projet Foundation](/static/img/project_structure.png)

### Module API

Contient la description, avec un vocabulatire fonctionnel, de ce que l'application **doit** implémenter come **usecases** et **ressources**.

Dans les packages qui sont proposés, vous retrouvez:

#### Package api.schema
Ce package contient le modèle de données (POJO) manipulés et produits par les usecases.

#### Package api.usecase
Ce package contient les interfaces décrivant les fonctionnalités du projet. Chaque usecase utilise un verbe en anglais pour clairement décrire 
la responsabilité.

En terme de bonnes pratiques, nous recommandons ce qui suit:

- Un verbe commençant par `Get` indique qu'il s'agit d'une opération de lecture (Query)
- Un verbe commençant par `On` indique qu'il s'git d'un événement (Event)
- Tous les autres usecases sont considérés comme des opérations d'écriture (Command)

```java Echo.java
package foundation.app.api.usecase;

import dev.soffa.foundation.core.UseCase;
import foundation.app.api.schema.EchoInput;
import foundation.app.api.schema.Message;

public interface Echo extends UseCase<EchoInput, Message> {}

```

La définition d'un UseCase est une simple interface qui étend `dev.soffa.foundation.core.UseCase`. 
En ouvrant l'interface, on peut donc avoir une idée générale de que cette opération est censée faire, quel est l'input et quel est l'output.

Il est donc recommandé de toujours choisir une terminologie fonctionnelle simple à comprendre : <a href="https://www.martinfowler.com/bliki/UbiquitousLanguage.html" target="_blank"><em>Ubiquitous Language</em></a>.


#### Package api.resources

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

La méthode `invoke` déclarée dans l'inteface `Resource` permet au développeur de faire le mapping entre l'opération et
le bone usecase.

!!!
Même si vous constatez des annotations SpringBoot à ce niveau, il ne s'agit que d'annotations. Les fichier correspodants ont été copier depuis le projet officiel Spring
et coller dans `foundation-api` pour ne pas avoir à inclure toute la dépendance Spring dans ce module API. 
L'interprétation de ces interfaces n'est faite que dans la partie `app-service`.
!!!



#### Importance du module API

Le module API permet de décrire le fonctionnement de votre service mais aussi de servir d'interface pour les clients. En effet, le module `api` pouvant être publié séparement dans un serveur nexus, 
peut être rajouté comme dépendance dans un autre service dans lequel le code suivant peut etre utilisé :

```java
/* Logique présent dans un service-a souhaitant consommer une ressource du service-b */
EchoResource client = RestClient.newInstance(EchoResource.class, "https://service-b");
Message response = client.echo(new EchoInput("Hello"), new Context());
```

Il n'y a donc aucune génération de client à prévoir lorsque votre ressource doit être consommer depuis un code Java/Kotlin.


### Module Core

Contient l'implémentation des **usecases** (qui contiennent les règles de gestion). Cette implémentation peut nécessister l'accès à des ressources externes au système. L'interface de cette ressource externe est alors créée dans ce module et l'implémentation déléguée à la couche service

### Module Service

Le module `app-service` contient l'implémentation de vos différents usecases à travers des `handlers` et autres classes support.


```java
package foundation.app.core.handler;

...
import javax.inject.Named;

@Named
public class DoEcho implements Echo {

    @Override
    public Message handle(EchoInput input, @NonNull Context context) {
        return new Message(input.getMessage());
    }

```

!!!
Foundation est construit sur SpringBoot. Toutefois, grâce à la fonctionnalit" `implementation` de gradle, le développeur n'a pas accès aux différentes classes Spring.
Ceci est fait par design pour encourager l'utilisateur de classes simplement définies ainsi que de classe telel que `javax.inject.Named` au lieu de `@Component`, `@Service` (...) de Spring.
!!!

## Plus d'exemples

Le projet `foundation-java-starter` vous permet de disposer d'une structure basique pour démarrer rapidement.
Nous vous invitons à consulter [foundation4j-samples](https://github.com/soffalabs/foundation4j-samples) pour avoir plus d'exemples sur des cas d'usages plus avancés d'un simple `Echo`.
