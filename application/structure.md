---
label: Structure applicative
order: 100
---

<picture>
  <source srcset="/static/img/overview_dark.png" media="(prefers-color-scheme: dark)">
  <img src="/static/img/overview.png">
</picture>

## Inbound Gateways 

Dans la terminologie de l'architecture hexagonale, un _Inbound Gateway_ représente un point d'entrée de l'application: moyen par lequel les fonctionnalités peuvent être consommées par un client.


### PubSub

Lorsque le module `pubsub` est activé, toutes les opérations que vous déclarez sont automatiquement consommables (via RabbitMQ, Kafka ou Nats en fonction de votre choix).
Le binding par défaut se base sur le nom de l'interface de votre opération. 
Toutefois, l'annotation `@Handle` permet de personnaliser le nom de l'événement à traiter.

```kotlin HandleNewApplication.kt
@Named
@Handle("ApplicationStarted")
class HandleNewApplication : OnApplicationCreated {

    override fun handle(input: Application, ctx: Context) {
        Log.info("A new application [%s] was created.", input.name)
    }

}
```

### Scheduler

Dans la version actuelle, Foundation conserve l'annotation `@Scheduled` de SpringBoot

```kotlin IntentWorker.kt
@Named
class IntentWorker {

    @Scheduled(...)
    fun processPaymentIntents() {
    }
}
```

Toutefois, l'API devrait évoluer pour offrir d'autres mécanismes

## Operation

Déclarer une opération commence par la définition de l'interface correspondante dans le package `-api`.<br />
L'interface `dev.soffa.foundation.core.Operation<Input, Output>` est générique et requiert la définition des classes `Input` ou `Output`
du Operation. Lorsque votre Operation n'accepte pas d'input, vous pouvez utiliser la classe `Void`.


```kt CreatePaymentIntent.kt
import dev.soffa.foundation.core.Operation

interface CreatePaymentIntent : Operation<CreatePaymentIntentInput, PaymentIntent>
```

L'implémentation du `Operation` (dans le module `-service`) surcharge la méthode `handle` avec les classes Input et Output définies dans la signature et
un paramètre suppélementaire de type `Context`. Ce second paramètre vous permet d'accéder à plusieurs informations relatives au contexte
de la requête en cours.

```kotlin DoCreatePaymentIntent.kt
@Named
class DoCreatePaymentIntent: CreatePaymentIntent {

    override fun handle(
            input: CreatePaymentIntentInput, 
            context: Context
        ): PaymentIntent {
    }
}

```


## Outbound Gateways

Lorsque l'implémentation d'une Operation a besoin d'accéder à des composants externes (base de données, communication avec un service externe, etc),
il est important de toujours passer par une interface pour respecter le pattern Port/Adapter.

Pour accéder à une base de données par exemple, l' Operation devrait ressembler à ceci:

```kotlin

@Named
class DoAddTodo(private val todos: TodoRepository) : AddTodo {

    @TenantRequired
    @Authenticated
    override fun handle(input: AddTodoInput, context: Context): Todo {
        val todo = Todo(
            IdGenerator.shortUUID("t"),
            input.content!!,
            TodoStatus.PENDING,
            Date.from(Instant.now())
        )
        return todos.insert(todo)
    }
```

L'interface `TodoRespoitory` est définie comme suit :

```kotlin
interface TodoRepository {

    fun insert(model: Todo): Todo

}
```

Le développeur peut ensuite définir l'implémentation correspondante dans le package `gateways.outbound` car il s'agit d'une communication
storate de l'application vers un composant externe.


