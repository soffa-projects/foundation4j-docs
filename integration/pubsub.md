---
title: PubSub
---

> Dépendance: **foundation-starter-pubsub**

Foundation propose un mécanisme simple pour communiquer avec :

- [ ] Kafka
- [x] RabbitMQ
- [x] Nats

## Configuration

#### Intégration Nats

Pour intégrer NATS à votre projet, la configuration suivante est requise :

```yaml
app.pubsub.enabled: true
app.pubsub.clients.default.addresses: nats://localhost:14222 #URL du serveur nats
```


#### Intégration RabbitMQ

Pour intégrer RabbitMQ à votre projet, la configuration suivante est requise :

```yaml
app.pubsub.enabled: true
app.pubsub.clients.default.addresses: amqp://guest:guest@localhost:5672
```

## Client PubSubMessenger


L'interface `dev.soffa.foundation.message.pubsub.PubSubMessenger` contient les différentes opérations pour interagir avec le broker configuré.
Les opérations sont les mêmes quel que soit le broker configuré.

```java
@Inject
private PubSubMessenger messenger;

// Recevoir des messages

messenger.subscribe("canal-01", message -> {
    // MessageHandler
});

// Transmettre des messages
messenger.publish("subject-01", MessageFactory.create("operation-test"));
messenger.publish("subject-01", MessageFactory.create("operation-test", ImmutableMap.of("id", "1625165267152")));


```