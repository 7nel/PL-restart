# CLAUDE.md — PL-restart’

Langue du projet : **français** (interface, commentaires, commits, README).

## Vue d'ensemble

**PL-restart’** est un prototype d'outil de classe : une **page web unique, sans serveur, sans compte, sans build**. L'élève signale un blocage sur une tâche scolaire, choisit une famille de cause, reçoit une carte de stratégie, puis dit si ça repart. Après trois « non », l'outil l'aide à formuler une demande d'aide précise.

- Confidentialité : **aucune donnée ne quitte le navigateur** (`localStorage`). Ne jamais ajouter de réseau, de télémétrie ni de service tiers.
- Licence : CC BY-NC 4.0.
- Même famille que PL-planif’, PL-lect’, PL-CPS’, PL-neurodev’ : même en-tête (monogramme PL, nom de l'outil), vert `#2f6f63`, Atkinson Hyperlegible et Lexend autohébergées, apostrophe courbe dans le nom.

## Structure

```
index.html   # toute l'application
sw.js, manifest.json, icon-*.png, fonts/   # hors ligne, installation, polices
README.md, LICENSE, CLAUDE.md
```

`index.html` est un squelette minimal dont tout le contenu est un unique `<script>` : `function boot() { … }` puis `boot()`. Le CSS est la chaîne `CSS_TEXT`, le DOM est construit par le JS. Sections repérées par `/* ---------- … ---------- */` : style, icons, contenu, local state, audio, DOM skeleton, moteur, écrans élève, espace enseignante, actions.

## Modèle

- **Contenu** (constantes) : `FAMILIES` (F1 à F5, plus `F0`), `LINES` (une réponse de l'élève = une suite de cartes `seq`, `then` = famille proposée après deux « non », `byType` = stratégies placées en tête selon la tâche), `QUESTIONS`, `CARDS` (C01 à C23, cartes génériques), `METHODS` (M01 à M14, stratégies du dossier de français : trois étapes, un « truc », annexe éventuelle).
- **Deux modes** : avec suivi (`U.task` défini) et coup de main (`U.free`, blocage non enregistré tant qu'il n'est pas rattaché à un élève ; `tid` reste `null`).
- **Stratégies** : état par élève dans `st.methods[id]` (0 pas encore vue, 1 guidée, 2 avec la carte, 3 seul). `available()` écarte une stratégie non vue et une carte générique dont le `twin` est connu ; `effectiveSeq()` calcule la suite réelle d'une ligne. `st.plan` active l'écran « Mon plan d'attaque » au démarrage (`task.plan`, `task.planHelped`).
- **État** `S` (clé `pl-restart-v1`) : `settings`, `labels` (libellés réécrits par l'enseignante), `students`, `tasks`, `blocks` (chaque blocage contient ses `tries`).
- **Règles** : au plus trois « non » par blocage, puis écran d'aide ; niveau d'aide par famille (N1 guidé, N2 et N3 choix par l'élève), ajusté dans `updateLevel()` ; deux garde-fous dans `blocked()`.
- Un essai : `{ card, origin: "proposee" | "choisie", at, dur, result: 1 | 0.5 | 0 | null }`, plus `mode` (1 à 3) et `peek` pour une stratégie.
- Issues d'un blocage (`end`) : `reprise`, `aide`, `passe` (« ? » dans la marge), `stop`, `abandon`, `annule`.
- Les noms, numéros et étapes des stratégies viennent du dossier de l'enseignante : ne pas les reformuler sans demande.

## Conventions

- Un seul fichier, JavaScript vanilla, style ES5 (`var`, fonctions nommées), guillemets doubles, indentation 2 espaces.
- Tout texte saisi inséré via `innerHTML` passe par `esc()`.
- `localStorage` toujours dans `try/catch`. Toute nouvelle propriété reçoit une valeur par défaut dans `normalize()` ; ne jamais renommer un champ exporté sans migration.
- Couleurs par variables CSS, définies en clair et en sombre.
- Côté élève : un écran, une question, douze mots au plus, gros boutons, bouton « Écouter ». Ni points ni récompenses.
- L'outil décrit des situations, jamais l'élève. Aucune mention de diagnostic.
- Changer `CACHE` dans `sw.js` à chaque publication.

## Vérification

- [ ] La page s'ouvre sans erreur dans la console (`file://` inclus)
- [ ] Ajouter un élève, lancer une tâche, parcourir un blocage jusqu'à la reprise, puis jusqu'à l'écran d'aide
- [ ] Coup de main sans élève (stratégies guidées), puis rattaché à un élève
- [ ] Élève avec stratégies réglées (guidée, avec la carte, seul) et plan d'attaque activé
- [ ] Rechargement : les données persistent, la tâche en cours peut être reprise
- [ ] Suivi, export `.json` et `.csv`, import d'une sauvegarde
- [ ] Clair et sombre, largeur téléphone et tablette
