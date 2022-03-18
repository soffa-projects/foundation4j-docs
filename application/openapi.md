---
label: OpenAPI
order: 99
---

## Redoc

En supposant que votre projet soit configué sur le port 8080, lorsque vous démarrez l'application, la age d'accueil
vous afficher la présentation de votre API avec [Redoc](https://github.com/Redocly/redoc)

![http://localhost:8080](/static/img/app_redoc.png)

### Configuration

```yaml
app.redoc.enabled: true # ou false
```

## OpenAPI

![http://localhost:8080/swagger-ui/index.html](/static/img/app_openapi.png)

### Configuration

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



```