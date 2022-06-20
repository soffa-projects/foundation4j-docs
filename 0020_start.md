---
label: Bien démarrer
order: 30
route: /start
icon: rocket
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


## Structure projet


Par défault, un projet créé avec le client `foundation` utilisera le template projet que nous recommandons.
Ce template est un projet Gradle avec deux modules : `api` et `service`.  


![Structure  d'un projet Foundation](/static/img/project_structure.png)



## Plugin gradle

Chaque projet Foundation inclut la dépendance au [plugin gradle](https://github.com/soffalabs/foundation-gradle-plugin).
Ce plugin permet de simplifier certaines configuration projet.

```kotlin
buildscript {
    dependencies {
        classpath("dev.soffa.foundation:foundation-gradle-plugin:1.0.14")
    }
}
```

**Liste des plugins**

| Plugin                                | Niveau    | Description   |
|:-                                     |:-         |:-             |
| `foundation.api.kotlin`               | module    | Applique les plugins `foundation.kotlin` et `foundation.test.junit5` + la dépendance `dev.soffa.foundation:foundation-api` |
| `foundation.api`                      | module    | Applique les plugins `foundation.java8` et `foundation.test.junit5` + la dépendance `dev.soffa.foundation:foundation-api` |
| `foundation.default-repositories`     | root      | Configure les repositories par défaut pour tous les sous-modules: `mavenLocal` et `mavenCentral` |
| `foundation.java8`                    | module    | Configure le module pour un développement avec Java8 + PMD |
| `foundation.java11`                   | module    | Configure le module pour un développement avec Java11 + PMD |
| `foundation.java17`                   | module    | Configure le module pour un développement avec Java17 + PMD |
| `foundation.kotlin`                   | module    | Configure le module pour un développement avec Kotlin et Java8+PMD |
| `foundation.lombok`                   | module    | Active le module Lombok sur le module |
| `foundation.maven-publish`            | module    | Active la publication vers un repository maven |
| `foundation.project.kotlin`           | root      | Applique le plugin `foundation.api.kotlin` au module `api` de votre projet;<br />Applique le plugin `foundation.service.kotlin` au module `service`;<br />Rajoute à votre module `service` la dépendance avec le service `api` |
| `foundation.project`                  | root      | Applique le plugin `foundation.api` au module `api` de votre projet;<br />Applique le plugin `foundation.service` au module `service`;<br />Rajoute à votre module `service` la dépendance avec le service `api` |
| `foundation.qa.checkstyle`            | module    | Applique le plugin `checkstyle` au module. Votre fichier `checkstyle.xml` doit être présent dans le dossier `config/qa/` |
| `foundation.qa.coverage.l1`           | module    | Configure la couverture de test à un min de 10% |
| `foundation.qa.coverage.l2`           | module    | Configure la couverture de test à un min de 20% |
| `foundation.qa.coverage.l3`           | module    | Configure la couverture de test à un min de 30% |
| `foundation.qa.coverage.l4`           | module    | Configure la couverture de test à un min de 40% |
| `foundation.qa.coverage.l5`           | module    | Configure la couverture de test à un min de 50% |
| `foundation.qa.coverage.l6`           | module    | Configure la couverture de test à un min de 60% |
| `foundation.qa.coverage.l7`           | module    | Configure la couverture de test à un min de 70% |
| `foundation.qa.coverage.l8`           | module    | Configure la couverture de test à un min de 80% |
| `foundation.qa.coverage.l85`          | module    | Configure la couverture de test à un min de 85% |
| `foundation.qa.coverage.l9`           | module    | Configure la couverture de test à un min de 90% |
| `foundation.qa.coverage.l95`          | module    | Configure la couverture de test à un min de 95% |
| `foundation.qa.coverage.lX`           | module    | Configure la couverture de test à 100% |
| `foundation.qa.pmd`                   | module    | Active le plugin `pmd` sur le module |
| `foundation.service.kotlin`           | module    | Appliquer les plugins `foundation.kotlin`, `foundation.springboot`<br />Rajouter les dépendances `dev.soffa.foundation:foundation-starter` et `dev.soffa.foundation:foundation-starter-test` |
| `foundation.service`                  | module    | Appliquer les plugins `foundation.java8`, `foundation.springboot`<br />Rajouter les dépendances `dev.soffa.foundation:foundation-starter` et `dev.soffa.foundation:foundation-starter-test` |
| `foundation.test.junit5`              | module    | Configure les tests avec Junit5 pour le module |
| `foundation.test.karate`              | module    | Configure les tests avec Karate pour le module |


## Fichier gradle.properties

```yaml gradle.properties

group = foundation.app # À adapter à votre projet
version = 1.0.0 # À adapter à votre projet

foundation.version = 0.9.5 # Version de Foundation à utiliser
#foundation.modules = data,pubsub # Modules foundation que vous souhaitez utiliser dans votre projet
foundation.qa.coverage = l3 # Niveau de couverture des tests
```

Le contenu par défaut de ce fichier de configuration devrait vous permettre de créer un service si vous n'avez pas besoin d'accéder à une base de données, un message broker, etc.

Si votre service a besoin de ces capacités, la ligne `foundation.modules` doit alors être décommentée avec les bonnes valeurs.

**Modules foundation disponible**

| Module    | Description   |
|:-         |:-             |
| data      | Module de gestion persistence (multi-tenant)                      |
| pubsub    | Module de connexion à des bus de messages: Nats, Kafka, RabbitMQ) |
| storage   | Module de connexion à un service Object Storage                   |
| tracing   | Module d'intégration à un service de tracing (ex: Zipkin)         |


## Plus d'exemples

Le projet `foundation-java-starter` vous permet de disposer d'une structure basique pour démarrer rapidement.
Nous vous invitons à consulter [foundation4j-samples](https://github.com/soffalabs/foundation4j-samples) pour avoir plus d'exemples sur des cas d'usages plus avancés d'un simple `Echo`.

