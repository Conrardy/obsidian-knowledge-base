---
applyTo: "03-research-inbox/**/*.md"
---

***
# Règles pour le dossier "03-research-inbox"

## Objectif

Le dossier `03-research-inbox` sert à collecter, organiser et traiter les notes de recherche en cours avant leur validation et classement définitif.

***

## Règles de gestion

- **renommer les fichiers**  
    Utiliser un format clair et descriptif pour les noms de fichiers, par exemple :  
    `sujet-principal.md`  enlève les éventuels prefixes.
    Exemple : 
    `note-gestion-du-temps.md` devient `gestion-du-temps.md`
    et `20250906-gestion-du-temps.md` devient `gestion-du-temps.md`

- **Création de note**  
    Chaque nouvelle note doit inclure :
    - Un en-tête avec la date de création et de modification au format `YYYYMMDDHHmm`.
    - Le statut de la note à"En cours".
    - Les tags pertinents.
    - Les liens vers les notes connexes, en utilisant le format Obsidian `[[nom-de-la-note]]`.

- **Formatage des dates**  
    Toujours utiliser le format `YYYYMMDDHHmm` pour les dates dans les métadonnées.

- **Liens**  
    Vérifier que tous les liens internes pointent vers des notes existantes et sont correctement formatés :  
    Exemple : `[[gestion-du-stress]]`

- **Séparation visuelle**  
    Utiliser `***` pour séparer les sections principales dans chaque note.

***

## Exemple de note conforme

``` markdown example de note
Date de création : 202509061833
Date de modification : 202509061833
Statut : Validé
Tags : #productivité #planification #gestion-du-temps  
MOC : Coaching
***
### Concept

Technique de planification assignant des créneaux temporels fixes à des activités précises, favorisant le focus mono-tâche.

### Mécanisme

- **Bloc dédié** : Période assignée à une tâche ou activité  
- **Planification** : Division de la journée et attribution des tâches  
- **Protection** : Respect strict des créneaux  
- **Flexibilité** : Ajustement selon les imprévus

### Applications

- Créneaux pour emails, réunions, rédaction  
- Gestion de projets complexes par segments  
- Équilibre entre travail et vie personnelle

### Questionnements

- Gestion des tâches imprévues ?  
- Outils d'implémentation ?  
- Adaptation à une routine hebdomadaire ?

### Liens

[[Matrice Eisenhower - Priorisation urgence-importance]] • [[Loi de Pareto - Principe 8020 en productivité]] • [[Méthode GTD - Système de gestion globale des tâches]] • [[Revue Hebdomadaire - Réflexion et planification cyclique]]

**Source :** Cal Newport, "Deep Work"

*** 
``` markdown example de note
Date de création : 202509061833
Date de modification : 202509061833
Statut : Validé
Tags : #productivité #planification #gestion-du-temps
MOC : Coaching
***
### Concept

Technique de planification assignant des créneaux temporels fixes à des activités précises, favorisant le focus mono-tâche.

### Mécanisme

- **Bloc dédié** : Période assignée à une tâche ou activité
- **Planification** : Division de la journée et attribution des tâches
- **Protection** : Respect strict des créneaux
- **Flexibilité** : Ajustement selon les imprévus

### Applications

- Créneaux pour emails, réunions, rédaction
- Gestion de projets complexes par segments
- Équilibre entre travail et vie personnelle

### Questionnements

- Gestion des tâches imprévues ?
- Outils d'implémentation ?
- Adaptation à une routine hebdomadaire ?

### Liens

[[Matrice Eisenhower - Priorisation urgence-importance]] • [[Loi de Pareto - Principe 8020 en productivité]] • [[Méthode GTD - Système de gestion globale des tâches]] • [[Revue Hebdomadaire - Réflexion et planification cyclique]]

**Source :** Cal Newport, "Deep Work"

```
