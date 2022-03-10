---
label: Choix structurants
---

Pour construire Foundation, il fallait faire des choix structurants pour que chaque entreprise puisse décider très rapidement
s'il y a un alignement avec sa stratégie interne.

Nous avons donc simplifié les choses et construit sur 3 choix structurants :

## Architecture hexagonale

Ce premier choix était le plus simple à faire car nous considérons que l'architecture hexagonale est un solide pilier
sur lequel une entreprise peut construire une stratégie logicielle long-terme.

L'objectif de cette architecture est de séparer, autant que faire se peut, le technique du fonctionnel en réduisant
au maximum la dépendance vis à vis d'un framework technique.

Pour cette raison, nous classons Foundation dans la catégorie **framework fonctionnel** plutôt que technique car la priorité,
comme on pourra le voir dans les sections suivantes, est donnée au **domaine métier**.

![](/static/img/hexagonal_architecture.png)

## SpringBoot

Nous faisons le choix de construire par dessus SpringBoot car c'est cetainement le framework Java le plus populaire en entreprise.
Toutefois, l'objectif de Foundation est de masquer au mieux le technique. Même si c'est quelque chose que SpringBoot fait déjà très
bien, nous souhaitons aller plus loin et offir une solution orientée "fonctionnel".

## Java 8

Java8 est encore très populaire en entreprise et avoir un code compatible avec cette version est le 3e et dernier choix strucurant de Foundation.