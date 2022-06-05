---
title: Envoi de mails
---


L'interface `dev.soffa.foundation.mail.EmailSender` encapsule les opérations nécessaires pour communiquer avec un serveur d'envoi de mails.


## La classe EmailSenderFactory

#### Envoi de mails en mode bouchon (Mocked)

Cette implémentation est très utile pour des tests unitaires et certaines tests d'intégration.

```java
EmailSender server = EmailSenderFactory.create(
    "faker://local", 
    "no-reply@foundation.dev"
);
```

#### Envoi de mails avec un serveur SMTP

Implémentation à utiliser lorsque vous vous interfacez directement avec un serveur STMP.

```java
EmailSender server = EmailSenderFactory.create(
    "smtp://user:pass@EmailSenderFactory.google.com:963?tls=true", 
    "no-reply@foundation.dev"
);
```


#### Envoi de mails avec Sendgrid

Implémentation à utiliser lorsque vous utilisez le très populaire service [Sendgrid.com](https://sendgrid.com/).

```java
EmailSender server = EmailSenderFactory.create(
    "sendgrid://ak092092012:@sendgrid.com", 
    "no-reply@foundation.dev"
);

EmailSender server = EmailSenderFactory.create(
    "sendgrid://sendgrid.com?apiKey=ak092092012", 
    "no-reply@foundation.dev"
);
```

## Intégration dans votre projet

Foundation vous permet de définir plusieurs clients Mails sous le préfixe `app.mail`

```yaml
app.mail.clients:
  default: smtp://foo:s3cret@smtp.google.com:587
  tenant1: faker://local
  tenant2: sendgrid://sendgrid.com?apiKey=12121212
  tenant3: sendgrid://sendgrid.com?apiKey=0EZOIEOZIO
```    

Une fois cette définition rajoutée à votre fichier de configuration, la syntaxe suivante vous permet d'accéder aux clients:

```java


@Named
public class EmailService {
    
    private Mailer mailer;

    public EmailService(Mailer mailer) { // Injection de dépendance
        this.mailer = mailer;
    }

    public void sendEmail(String clientId) {
        EmailSender sender = mailer.getClient(clientId);
        ...
    }

}

```
