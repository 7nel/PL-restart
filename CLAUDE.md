# CLAUDE.md — PL-restart’

Langue du projet : **français** (interface, commentaires, commits, README).

## Vue d'ensemble

**PL-restart’** est un prototype d'outil de classe : une **page web unique, sans serveur, sans compte, sans build**. L'élève signale un blocage sur une tâche scolaire, choisit une famille de cause, reçoit une carte de stratégie, puis dit si ça repart. Après trois « non », l'outil l'aide à formuler une demande d'aide précise.

- Confidentialité : **aucune donnée ne quitte le navigateur** (`localStorage`). Ne jamais ajouter de réseau, de télémétrie ni de service tiers. Seule exception de téléchargement : la voix naturelle, fichiers du dossier `voix/` du même site (jamais un autre domaine).
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

- **Contenu** (constantes) : `FAMILIES` (F1 à F5, plus `F0`), `LINES` (une réponse de l'élève = une suite de cartes `seq` ; `then` = famille proposée après deux « non » ; `pre` = règles `{ when, not, ids, seq }` qui placent des stratégies en tête selon la matière `subject`, la tâche `type` et le lieu `place` ; `only` = réponse visible dans ce seul contexte), `QUESTIONS`, `CARDS` (C01 à C23, cartes générales), `METHODS` (M01 à M14 : dossier de français, `dom: "fr"` ; X01 à X07 maths, R01 à R06 réviser, L01 à L06 langues, E01 à E06 fatigue et stress ; trois étapes, parfois un « truc », une annexe, `replaces`, `stop`).
- **Format unique** : `card(id)` rend toute carte ou stratégie sous la même forme (`dom`, `title`, `label`, `steps`, `truc`, `min`, `stop`, `replaces`, `twin`).
- **Contexte** : `context(b)` = matière, tâche, lieu (tâche en mode suivi, sinon réponses données en route) ; `needsCtx()` ne pose la question que si elle change la stratégie ; `FAMILY_SUBJECT` = familles qui demandent la matière avant leur question.
- **Deux modes** : avec suivi (`U.task` défini) et coup de main (`U.free`, blocage non enregistré tant qu'il n'est pas rattaché à un élève ; `tid` reste `null`).
- **Stratégies** : état par élève dans `st.methods[id]` (0 pas encore vue, 1 guidée, 2 avec la carte, 3 seul) ; par défaut 0 pour le dossier de français, 1 pour tout le reste. `available()` écarte une stratégie non vue et une carte générale dont le `twin` est connu ; `effectiveSeq()` calcule la suite réelle d'une ligne (règles `pre`, doublons, `replaces`).
- **Retrait de l'aide** : `suggestions(sid)` propose (3 reprises de suite, 2 échecs de suite, 2 cartes rouvertes au niveau « seul ») ; seule l'enseignante valide (`sugOk`, `sugNo`, `st.msince`, `st.mdismiss`). Ne jamais changer un état sans validation.
- **Écrans par élève** : `st.plan` (« Mon plan d'attaque » au démarrage : `task.plan` ; `planFirst()` donne les quatre stratégies montrées d'abord, `planIds()` la liste complète ; `task.planHelped` n'est plus rempli mais reste dans les données), `st.check` (« Avant de rendre » : `task.checked`).
- **Plan cliquable** : une puce du plan (écran de travail) lance `planTry(id)` : nouveau blocage de cause `plan`, stratégie guidée, puis « Ça repart ? » ; après un « non », l'élève choisit la cause. **Cartes `home: true`** (« Pause courte ») : écartées par `available()` hors lieu « maison » (se lever n'est pas possible en classe) ; F2 propose aussi « Je suis fatigué » (F2f) et « Je stresse » (F2g).
- **Démarrage** : F2q2 propose « Je n’arrive pas à m’y mettre » (F2d, commence par C24 « Je prépare ma place »), fatigué (F2f), stress (F2g), « Je n’ai pas envie » (F2h, derrière « Autre chose »). Les cartes avec `durs` (C12 « Tranche minutée » : 5, 10 ou 15 min) laissent l’élève choisir la durée (`U.dur`, notée `dm` dans l’essai). E01 et E02 remplacent déjà C11 : la pause courte n’apparaît donc presque plus.
- **Adulte disponible** : `task.adult` et `block.adult` valent `true`, `false` ou `null` (pas encore dit). Tout passage à l'écran d'aide passe par `goHelp()` : question si `null`, puis, sans adulte, une stratégie de plus (`extraCard()`, cartes `ALONE` par famille, `block.extra`), puis `screenHelp()` et sa variante « Prépare ta question ». Ne jamais appeler `screenHelp()` directement depuis le moteur.
- **État** `S` (clé `pl-restart-v1`) : `settings` (dont `place`, dernier lieu choisi, et `autoLevel`, désactivé par défaut), `labels` (libellés réécrits par l'enseignante), `students`, `tasks`, `blocks` (chaque blocage contient ses `tries`).
- **Règles** : au plus trois « non » par blocage, puis écran d'aide ; niveau par famille (N1 guidé, N2 et N3 choix par l'élève), réglé à la main ou par `updateLevel()` si `autoLevel` ; deux garde-fous dans `blocked()`.
- Un essai : `{ card, origin: "proposee" | "choisie", at, dur, result: 1 | 0.5 | 0 | null }`, plus `mode` (1 à 3) et `peek` pour une stratégie.
- Issues d'un blocage (`end`) : `reprise`, `aide`, `passe` (« ? » dans la marge), `stop`, `abandon`, `annule`.
- Les noms, numéros et étapes des stratégies viennent du dossier de l'enseignante (français) ou ont été validés par elle (quatre autres domaines) : ne pas les reformuler sans demande.

## Outils d'apaisement (v6)

- `TOOLS` (A01 à A04, repris du site PL-CPS’) : cartes comme les autres (`card(id)` renvoie `guide` et `dom: "calme"`), hors `METHODS` (pas d'état par élève) et hors `strategyIds()`. `renderTry()` ouvre `startGuide()` : minuteur `requestAnimationFrame` (`guideDef`, `paintGuide`, `runGuide`, `finishGuide`), nettoyé par `clearTimers()`.
- « Ça ne va pas du tout » : `screenCalm()` (`CALM_TOOLS`), puis `tried()` → `goHelp()` comme avant. Lignes `F3d` (« Je stresse »), `F3c` et `F4d` y font aussi appel.
- Durées et consignes d'origine ; titres élève et phases à valider par l'enseignante : ne pas les reformuler sans demande.

## Données entre appareils (v5)

- `st.cfgAt` : date du dernier réglage de l'enseignante pour l'élève (`touchCfg(st)` à chaque réglage) ; la fusion garde la version la plus récente de `CFG_FIELDS` ; `pinned` : union.
- `exportData(sid)` : jamais `settings.pin`. `mergeData()` : ne supprime rien ; tâches et blocages par id (`blockScore`, `taskScore`), notes enseignante conservées ; libellés complétés seulement ; réglages de l'appareil jamais importés (sauf sauvegarde complète sur appareil vide, sans le code).
- Voix : `settings.voice` ("" = automatique, "piper" ou `voiceURI`) et `settings.rate` ; Piper dans `voix/` (cache `plrestart-voix-v1`, import map dans `<head>`) ; notices dans `NOTICE.md`.
- `st.code` : code facultatif de l'élève (`screenStudentCode`, `codeOk`), fait partie de `CFG_FIELDS` donc voyage dans l'export de l'élève ; le code enseignante l'ouvre aussi. Code de départ de l'enseignante `DEFAULT_PIN` ; l'ancien (`OLD_DEFAULT_PIN`) est migré dans `normalize()`.
- Le code de l'espace enseignante est en clair (`settings.pin`), lettres autorisées : choix assumé.

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
- [ ] Coup de main en maths, en langues, en révision, « fatigué » en classe puis à la maison : la bonne stratégie, la bonne question de contexte
- [ ] Élève avec stratégies réglées (guidée, avec la carte, seul), plan d'attaque et « Avant de rendre » activés
- [ ] Adulte « Non » au démarrage : stratégie de plus, puis « Prépare ta question » ; adulte non renseigné : la question arrive au moment de l'aide
- [ ] Trois reprises avec une stratégie : la suggestion apparaît dans « Suivi », « Valider » change l'état
- [ ] Rechargement : les données persistent, la tâche en cours peut être reprise
- [ ] Suivi, export `.json` (complet et par élève, sans le code), `.csv`, import fusionné (réimport sans doublon, réglage le plus récent gagne, ancienne sauvegarde)
- [ ] Voix : choix et débit conservés, voix naturelle téléchargée puis lue, retirée ; `sw.js` ne supprime que les caches `plrestart-`
- [ ] Clair et sombre, largeur téléphone et tablette
