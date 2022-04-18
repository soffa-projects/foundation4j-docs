---
order: 95
label: Observabilité
---

Chaque application Foundation embarque un certain nombre de capacités activées par défaut. Dans la catégorie **Observabilité** on
retrouve les éléments suivants :

## Health check

Pour avoir un état du service, l'uri `/health` est disponible en alias de `/actuator/health` (configuration par défaut SpringBoot)

```json /health
{
  "status": "UP",
  "groups": [
    "liveness",
    "readiness"
  ]
}
```


## Métriques applicatives

Votre application embarque par défault une exposition pour Prometheus disponible sur `/metrics` en alias de `/actuator/prometheus` (configuration par défaut SpringBoot)

```text /metrics
# HELP executor_queued_tasks The approximate number of tasks that are queued for execution
# TYPE executor_queued_tasks gauge
executor_queued_tasks{name="applicationTaskExecutor",} 0.0
executor_queued_tasks{name="taskScheduler",} 0.0
# HELP executor_pool_core_threads The core number of threads for the pool
# TYPE executor_pool_core_threads gauge
executor_pool_core_threads{name="applicationTaskExecutor",} 8.0
executor_pool_core_threads{name="taskScheduler",} 1.0
# HELP system_cpu_count The number of processors available to the Java virtual machine
# TYPE system_cpu_count gauge
```

## Logs

Foundation embarque `slf4j` pour la gestion des logs. Toutefois, au lieu d'utiliser directement le classe `org.slf4j.LoggerFactory` pour créer votre logger,
remplacer par `dev.soffa.foundation.commons.Logger` vous permet de bénéficier de quelques avantages dont l'injection du contexte de la requête dans vos logs.
Chaque log produit contiendra donc:
- Le nom de l'application appelante
- Le tenant actif (si votre service est multitenant)
- Les informations de trace `traceId` et `spanId`
- L'identifiant de l'utilisateur connecté (si applicable)

```java
private static final Logger LOG = Logger.get(Application.class)

LOG.trace();
LOG.info();
LOG.debug();
LOG.warn();
LOG.error();
```

Lorsque votre service démarre avec l'un des profils `production`, `prd` ou `prod`.



## Exceptions

Les stacktraces en Java sont très verbeux et il est souvent difficile d'identifier rapidement la bonne information.
Foundation intègre la librair [MgntUtils](https://github.com/michaelgantman/Mgnt) pour supprimer des stacktraces les lignes qui ne sont pas pertinents.

Au démarrage de votre application, le package principal est détecté et utilisé pour filter les traces d'erreurs.

Lorsqu'une erreur survient lors d'un appel HTTP, le résultat est présenté en format JSON comme suit:

> Le champs `trace` est absent lorsque votre application démarre avec le profil `production`, `prd` ou `prod`.

```json

{
    "timestamp": 1647613864069,
    "source": "foundation-starter",
    "kind": "TechnicalException",
    "status": 500,
    "message": "Controlled exception",
    "prod": false,
    "traceId": "4bbf888a-aecd-40b6-9941-3547b40eaedb",
    "spanId": "d81bc41a-52e0-4158-b4f2-7c632649fdeb",
    "trace": [
        "",
        "dev.soffa.foundation.error.TechnicalException: Controlled exception",
        "\tat foundation.app.core.handler.DoEcho.handle(DoEcho.java:17)",
        "\tat foundation.app.core.handler.DoEcho.handle(DoEcho.java:12)",
        "\tat foundation.app.core.handler.DoEcho$$FastClassBySpringCGLIB$$e59a833b.invoke(<generated>)",
        "\tat org.springframework.cglib.proxy.MethodProxy.invoke(MethodProxy.java:218)",
        "\t...",
        "\tat foundation.app.core.handler.DoEcho$$EnhancerBySpringCGLIB$$5268e711.handle(<generated>)",
        "\tat dev.soffa.foundation.spring.service.OperationDispatcher.dispatch(OperationDispatcher.java:19)",
        "\t...",
        "\tat foundation.app.api.resource.EchoResource.echo(EchoResource.java:36)",
        "\tat java.base/java.lang.invoke.MethodHandle.invokeWithArguments(MethodHandle.java:509)",
        "\t..."
    ]
}
```

## Traces distribuées (Distributed tracing)

Les traces distribuées sont présentées dans la section [Integration/Tracing](../integration/tracing.md)

## Pistes d'audit

TODO