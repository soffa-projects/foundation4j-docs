---
label: Module Service
order: 99
---


Le module `app-service` contient l'implémentation de vos différentes opérations à travers des `handlers` et autres classes support.


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
Foundation est construit sur SpringBoot. Toutefois, grâce à la fonctionnalité `implementation` de gradle, le développeur n'a pas accès aux différentes classes Spring.
Ceci est fait par design pour encourager l'utilisateur de classes simplement définies (POJO) ainsi que de classes telles que `javax.inject.Named` au lieu de `@Component`, `@Service` (...) de Spring.
!!!

## Handler

> Work in progress


## Port

> Work in progress

## Service


> Work in progress
