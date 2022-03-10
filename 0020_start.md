---
label: Bien démarrer
order: 30
route: /start
---

!!!
Nous recommandons l'utilisation de Foundation si vous souhaitez construire un projet entreprise long terme (de taille moyenne ou grande) avec
une importante dans la maintenabilité et la qualité.
!!!

Pour démarrer un projet avec Foundation4j, nous recommandons d'utilsier le client [foundation-cli](https://github.com/soffalabs/foundation-cli).

- [Télécharger le client pour Mac](https://github.com/soffalabs/foundation-cli/releases/download/v1.1.2/foundation-mac)
- [Télécharger le client pour Windows](https://github.com/soffalabs/foundation-cli/releases/download/v1.1.2/foundation.exe)
- [Télécharger le client pour Linux](https://github.com/soffalabs/foundation-cli/releases/download/v1.1.2/foundation)

> Assurez-vous que le client est dans le PATH pour pouvoir exécuter la commande `foundation-cli` depuis n'importe quel dossier


## Créer un projet Java


```bash
foundation java \
    --name [nom-projet] \
    --group [groupId] \
    --package [package-de-base-du-projet]
```

La commande ci-dessus permet de créer un noueau projet où :

- `nom-project` est le nom du projet et du dossier qui sera créé pour contenir le code source (il est recommandé ici d'utiliser un nom en minuscules sans espace)
- `groupId` Le groupId de votre projet 
- `package-de-base-du-projet` Le package racine de votre projet


`foundation java --name hello --group com.company --package com.company.app`

La commande précente génère un projet **hello** avec la structure suivante :


![Structure d'un nouveau projet Foundation4j](/static/img/hello_project_structure.png)

## Créer un projet Kotlin

La commande `foundation kotlin` sera bientôt rajoutée au client.