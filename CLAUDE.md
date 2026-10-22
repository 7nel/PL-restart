# CLAUDE.md — PL-restart’

Langue du projet : **français** (interface, commentaires, commits, README).

## Vue d'ensemble

**PL-restart’** est un prototype d'outil de classe : une **page web unique, sans serveur, sans compte, sans build**. L'élève signale un blocage sur une tâche scolaire, choisit une famille de cause, reçoit une carte de stratégie, puis dit si ça repart. Après trois « non », l'outil l'aide à formuler une demande d'aide précise.

- Confidentialité : **aucune donnée ne quitte le navigateur** (`localStorage`). Ne jamais ajouter de réseau, de télémétrie ni de service tiers. Seule exception de téléchargement : la voix naturelle, fichiers du dossier `voix/` du même site (jamais un autre domaine).
- Licence : CC BY-NC 4.0.
- Même famille que PL-planif’, PL-lect’, PL-CPS’, PL-neurodev’ : même en-tête (monogramme PL, nom de l'outil), vert `#2f6f63`, fond chaud `#f4f0e8` (charte v2.2, appliquée le 2026-10-15), Atkinson Hyperlegible et Lexend autohébergées, apostrophe courbe dans le nom.

## Structure

```
index.html   # toute l'application
sw.js, manifest.json, icon-*.png, fonts/   # hors ligne, installation, polices
README.md, LICENSE, CLAUDE.md
```

`index.html` est un squelette minimal dont tout le contenu est un unique `<script>` : `function boot() { … }` puis `boot()`. Le CSS est la chaîne `CSS_TEXT`, le DOM est construit par le JS. Sections repérées par `/* ---------- … ---------- */` : style, icons, contenu, local state, audio, DOM skeleton, moteur, écrans élève, espace enseignante, actions.

## Modèle

- **Contenu** (constantes) : `FAMILIES` (F1 à F5, plus `F0`), `LINES` (une réponse de l'élève = une suite de cartes `seq` ; `then` = famille proposée après deux « non » ; `pre` = règles `{ when, not, ids, seq }` qui placent des stratégies en tête selon la matière `subject`, la tâche `type` et le lieu `place` ; `only` = réponse visible dans ce seul contexte), `QUESTIONS`, `CARDS` (C01 à C27, cartes générales ; C25 « La première phrase » et C26 « Je refais le début » ajoutées le 2026-10-22 pour remplacer des consignes circulaires ; C04 « Couper en 3 » est passée dans « C’est trop long »), `METHODS` (M01 à M14 : dossier de français, `dom: "fr"` ; X01 à X07 maths, R01 à R06 réviser, L01 à L06 langues, E01 à E06 fatigue et stress ; trois étapes, parfois un « truc », une annexe, `replaces`, `stop`).
- **Relecture du 2026-10-22** : `RETIRED` (C05, C07, C11, C18, M13, L03) : définitions gardées pour l'historique, `card().active` faux, absentes des parcours, du plan et des listes enseignante. Champ `ask` (C03, C08, C13, C17, X04, M14) : question Oui / Non avant la carte (`screenCardAsk`, `cardAsk`, `b.asked[id]`) ; « Non » passe à la carte suivante sans essai, `usable()` écarte la carte pour ce blocage. Champ `homeText` (C01, C09) : texte à la maison. C12 « Tranche minutée » et C27 « Chercher sur internet » sont `home: true` ; `needsCtx()` demande le lieu dès qu'une ligne contient une carte `home`. Champ `ifNo` (C27 : `"C17"`) : carte de repli, disponible seulement après « Non » à la question de la carte nommée ; gardée dans `effectiveSeq()`. Réponse « Ce que j'entends » (F1f) supprimée. M14 : annexe `schema` (`SCHEMA`, couleurs attribuées par Claude à la demande de l'enseignante).
- **Format unique** : `card(id)` rend toute carte ou stratégie sous la même forme (`dom`, `title`, `label`, `steps`, `truc`, `min`, `stop`, `replaces`, `twin`).
- **Contexte** : `context(b)` = matière, tâche, lieu (tâche en mode suivi, sinon réponses données en route) ; `needsCtx()` ne pose la question que si elle change la stratégie ; `FAMILY_SUBJECT` = familles qui demandent la matière avant leur question.
- **Deux modes** : avec suivi (`U.task` défini) et coup de main (`U.free`, blocage non enregistré tant qu'il n'est pas rattaché à un élève ; `tid` reste `null`).
- **Stratégies** : état par élève dans `st.methods[id]` (0 pas encore vue, 1 guidée, 2 avec la carte, 3 seul) ; par défaut 0 pour le dossier de français, 1 pour tout le reste. `available()` écarte une stratégie non vue et une carte générale dont le `twin` est connu ; `effectiveSeq()` calcule la suite réelle d'une ligne (règles `pre`, doublons, `replaces`).
- **Retrait de l'aide** : `suggestions(sid)` propose (3 reprises de suite, 2 échecs de suite, 2 cartes rouvertes au niveau « seul ») ; seule l'enseignante valide (`sugOk`, `sugNo`, `st.msince`, `st.mdismiss`). Ne jamais changer un état sans validation.
- **Écrans par élève** : `st.plan` (« Mon plan d'attaque » au démarrage : `task.plan` ; `planFirst()` donne les quatre stratégies montrées d'abord, `planIds()` la liste complète ; `task.planHelped` n'est plus rempli mais reste dans les données), `st.check` (« Avant de rendre » : `task.checked`).
- **Écran de travail** (`screenWork`) : plan en cartes numérotées au-dessus de « Je bloque », « J'ai fini » séparé par un pointillé, en bas « Adulte : oui / non » (`screenAdultWork`) et « Comment ça marche ? ». « Prépare ta question » et « Montre cet écran à un adulte » préremplissent la cause (`famLabel` + réponses) et les cartes essayées. Plus d'agrandissement de projection (retiré le 2026-10-22, outil non projeté). « Suivi » sans blocage : trois tuiles et une phrase.
- **Guide « Comment ça marche ? »** : `GUIDE` (quatre écrans, douze mots au plus par phrase), `screenGuide(i)`, ouvert par `guideLink(from)` (accueil, écran de travail) et par « Montrer le guide à la classe » (`tabAide`) ; `U.guideFrom` (`home`, `work`, `teacher`) décide du retour. Pas de mouvement, pas d'obligation au premier lancement. Textes proposés, à valider par l'enseignante.
- **Plan cliquable** : les puces du plan sont au-dessus de « Je bloque » (on les fait avant de bloquer) ; une puce lance `planTry(id)` : blocage enregistré de cause `plan` (suivi inchangé), stratégie guidée, puis « Tu comprends mieux ? » (`planResult()`, et non « Ça repart ? »). Oui / Un peu : « Étape réussie » (`end: reprise`), « Je continue ». Pas encore : retour à l'écran de travail (`end: abandon`), sans choix de cause. **Cartes `home: true`** (« Pause courte ») : écartées par `available()` hors lieu « maison » (se lever n'est pas possible en classe) ; F2 propose aussi « Je suis fatigué » (F2f) et « Je stresse » (F2g).
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
- Côté élève : un écran, une question, douze mots au plus, gros boutons, bouton « Écouter ». Ni points, ni scores, ni récompenses matérielles.
- Renforcement positif voulu quand l'élève essaie (« Bravo pour ton effort », « Tu as bien essayé ») : il porte sur l'essai, jamais sur le résultat ni sur la personne. Choix de l'enseignante (2026-10-09), qui remplace l'ancien « ni récompenses ».
- Variables CSS propres à ce projet (noms différents de la famille PL, assumés) : `--surface-alt` (= `--surface-2`), `--border` (= `--line`), `--muted` (= `--ink-2`), `--on-accent` (= `--accent-ink`), `--display` (Lexend). Teintes ajoutées : `--warm` / `--warm-soft` (ocre : « Un peu », astuces `.tip` et « truc » des cartes, panneaux d'avertissement), `--calm` / `--calm-soft` (bleu apaisant, réservé à « Ça ne va pas du tout » et aux outils d’apaisement, pour qu’il ne se confonde pas avec l’ocre d’avertissement), `--red` / `--red-soft` (bouton `.danger`, notes d'erreur, étiquette `.tag.ko` ; l'icône de « Pas encore » est en `--muted`, pas en rouge). Pas d'équivalent exact de `--warn` : `--warm` en tient lieu ; écart déclaré dans « Écarts assumés ».
- Contours de champs et de boutons secondaires : `--line-ctl` de la charte, pas `--border`. Jaune `--mark` : réservé aux repères qui sautent aux yeux (un seul par écran, texte en `--mark-ink`) ; seul usage dans cet outil : le repère « tu es ici » du fil des étapes et des guides d'apaisement (contour encre et libellé, voir « Direction visuelle élève »). Le texte n'est jamais posé sur le jaune.
- L'outil décrit des situations, jamais l'élève. Aucune mention de diagnostic.
- Changer `CACHE` dans `sw.js` à chaque publication.

## Direction visuelle élève : « le nœud et le fil » (2026-10-22)

Décision de l'enseignante : cet outil n'est pas projeté et doit plaire aux élèves et les motiver. Les écrans élève (hors `.wide`, donc hors espace enseignante) quittent la sobriété de la charte v2.2 ; les invariants (contraste, polices locales, aucune donnée, public dys / allophone) restent.

- **Idée** : un blocage est un nœud (`knotHtml`, quatre états `KNOT` de même structure, `knotX(e, step)`). Il se desserre à chaque essai et à chaque étape (`animateD`, 380 ms), reste serré tant que ça ne repart pas, file droit seulement à « Tu es reparti ! » (`THREAD`). Le fil des étapes (`trailHtml` : la cause, essai 1 à 3) porte le repère jaune, qui glisse (0,35 s). Pas de points, de scores ni de classement : le progrès se voit dans la forme.
- **Tons d'écran** : `show(html, { tone })` écrit `body[data-tone]` : `deep` (je cherche : questions), `calm` (je me calme : apaisement, « Ça ne va pas du tout » sans adulte), `light` (je fais : défaut), `win` (ça repart : « Tu es reparti ! », « C'est noté. »). `deep` et `calm` redéfinissent les jetons (`--bg`, `--ink`, `--accent`…) sur le `body` : les composants se recolorent seuls. Les écrans larges de l'espace enseignante n'ont pas de ton.
- **Aides au rendu** : `title(text, stage, knotOnly)` (stage : 0 la cause, 1 à 3 essai ; sans stage, pas de nœud ni de fil), `essaiNow()`, `stageNow()`, `stageTry()` (rien pour une stratégie du plan d'attaque), `kwHtml()` (mots d'action en majuscules repris en gras Lexend). Classe `.st` sur la page élève ; `.nth` pour « 2 sur 3 ».
- **Composants** : choix en liste sur le fil (rangées à filet, pas de cartes ; l'icône reste seulement où elle porte un sens : Oui / Un peu / Pas encore, « Terminé »), boutons principaux à appui « touche », carte de stratégie sans cadre (étapes sur le fil, étape en cours en grand, « truc » en barre ocre, annexe repliée avec « + »), feuille « Montre cet écran à un adulte » (seul panneau encadré), guides d'apaisement (carré tracé comme un fil, disque plein pendant « Serre fort », barre avec repère).
- **Retouches après critique (2026-10-22)** : écran d'aide allégé (« Tu as bien essayé » mène, feuille et consigne regroupées, mots inchangés) ; grand « 1 sur 3 » retiré (le fil porte l'information, le texte reste pour les lecteurs d'écran) ; plus de barres latérales de couleur (le « truc » et le panneau « Ça ne va pas du tout » prennent un rond sur le fil) ; « Oui / Un peu / Pas encore » : l'icône est l'arrêt sur le fil ; liens de pied d'écran en boutons secondaires ; l'écran de travail garde un nœud droit (il s'emmêle quand on bloque). Point resté ouvert : l'ombre pleine des gros boutons (appui « touche ») est un choix de la direction, la grille de qualité d'impeccable la déconseille hors monde néobrutaliste ; à trancher avec l'enseignante.
- Maquettes de référence (hors dépôt, dossier temporaire de la session du 2026-10-22) : directions A, B, C, puis D ; la v2 et les écrans d'aide, d'apaisement et de fin ont servi de modèle.
- Non vérifié : effet auprès d'élèves, rendu sur tablette réelle, lecteur d'écran, daltonisme (la forme et l'étiquette portent l'information, jamais la couleur seule : station pleine / cerclée / vide, disque plein ou vide).

## Écarts assumés avec la charte commune

État au 2026-10-15 : la palette de la charte v2.2 est appliquée à ce dépôt (neutres chauds, `--line-ctl`, `--red` aligné sur `--danger`). Restent les écarts ci-dessous, relus dans le code.

| Variable | Valeur dans le code | Équivalent charte | Raison, suite |
|---|---|---|---|
| `--surface-alt`, `--border`, `--on-accent` | mêmes valeurs que `--surface-2`, `--line`, `--accent-ink`, sauf `--on-accent` sombre `#12201d` | `--surface-2`, `--line`, `--accent-ink` | Anciens noms, assumés. Migration à décider séparément. |
| `--muted` | `#435452` (clair), `#b3b6ab` (sombre) | `--ink-2` `#52635f` (clair), `#b3b6ab` (sombre) | Ancien nom ; gris de texte plus foncé que la charte en clair, pour les petits textes de l'élève. |
| `--warm`, `--warm-soft` | `#b97a3d` (clair), `#e3a768` (sombre) ; mélange de 20 % | `--warn` `#8f520f` (clair), `#e6b366` (sombre) | Ocre propre à l'outil (« Ça ne va pas du tout », « Un peu », astuces). Deux options à décider : aligner sur `--warn`, ou garder `--warm` comme teinte déclarée. Sur le fond chaud clair, `#b97a3d` fait 3,12:1 : à ne pas utiliser pour du texte courant. |
| `--calm`, `--calm-soft` | `#3f6f8f` (clair), `#8bb7d6` (sombre) ; mélange de 16 % | aucun | Bleu apaisant, réservé à « Ça ne va pas du tout » et aux outils d'apaisement (ajouté le 2026-10-15, hors socle). Teinte propre à l'outil, déclarée ici ; à valider dans la charte comme teinte de projet. Contraste calculé : 4,76:1 sur `--bg` en clair, 7,54:1 en sombre. |
| `--red`, `--red-soft` | `#a03a2a` (clair), `#eb9b8b` (sombre) ; mélange de 14 % | `--danger` (mêmes valeurs) | Ancien nom seulement : valeurs alignées sur la charte v2.2. |
| `--deep`, `--on-deep` | clair : mélange 62 % sauge / encre, texte `--bg` (6,97:1) ; sombre : mélange 30 % sauge / fond, texte `--ink` (7,38:1) | `--deep` : sombre 45 % (5,25:1) | Aplat des écrans de question élève (la charte le réserve à la vitrine). Sombre assombri à 30 % pour le contraste du texte. |
| `--calm-deep` | clair : mélange 62 % `--calm` / encre (texte 6,61:1) ; sombre : 30 % `--calm` / fond (7,28:1) | aucun | Aplat des écrans d'apaisement, dérivé de `--calm` comme `--deep` l'est de la sauge. |
| `--mark` (jaune `#FFD83D`) | repère « tu es ici » du fil des étapes, un seul par écran | usage réservé au repère de lecture de PL-lect' | Nouvel usage : toujours aplat avec contour encre de 4 px et libellé écrit à côté (1,22:1 sur fond clair, ne porte pas seul l'information). |
| `--panel`, `--panel-ink`, `--rule` | copies de `--surface`, `--ink` et mélange 30 % de `--ink` | aucun | Valeurs d'origine conservées quand le ton `deep` ou `calm` redéfinit `--surface` et `--ink` (feuille à montrer, panneau « Ça ne va pas du tout ») ; filets du fil. |
| Projection | agrandissement automatique retiré ; colonne élève jusqu'à 860 px | la charte vise la projection | Décision du 2026-10-22 : outil non projeté. |
| Mouvement élève | nœud et repère 0,35 à 0,38 s ; fil de « Tu es reparti ! » 1 s ; appui des gros boutons 0,1 s | « aucun mouvement hors minuteur et lecture » sur les écrans élève | Mouvement à valeur pédagogique (progrès du blocage), supprimé avec `prefers-reduced-motion`. |
| Ombre pleine | `0 7px 0 rgba(0,0,0,.3)` sous `.big`, `0 5px 0` sous `.btn.primary` et le panneau « Ça ne va pas du tout » | `--shadow` doux | Appui « touche de clavier ». |

Les valeurs de contraste de ce tableau sont calculées par formule, pas mesurées avec un outil tiers.

## Vérification

- [ ] La page s'ouvre sans erreur dans la console (`file://` inclus)
- [ ] Ajouter un élève, lancer une tâche, parcourir un blocage jusqu'à la reprise, puis jusqu'à l'écran d'aide
- [ ] Coup de main sans élève (stratégies guidées), puis rattaché à un élève
- [ ] Coup de main en maths, en langues, en révision, « fatigué » en classe puis à la maison : la bonne stratégie, la bonne question de contexte
- [ ] Élève avec stratégies réglées (guidée, avec la carte, seul), plan d'attaque et « Avant de rendre » activés ; puces du plan au-dessus de « Je bloque », fin « Tu comprends mieux ? »
- [ ] Adulte « Non » au moment de l'aide : stratégie de plus, puis « Prépare ta question » ; en coup de main, aucune question d'adulte avant la première stratégie
- [ ] Trois reprises avec une stratégie : la suggestion apparaît dans « Suivi », « Valider » change l'état
- [ ] « Comment ça marche ? » : depuis l'accueil, l'écran de travail et l'espace enseignante, retour au bon endroit
- [ ] Rechargement : les données persistent, la tâche en cours peut être reprise
- [ ] Suivi, export `.json` (complet et par élève, sans le code), `.csv`, import fusionné (réimport sans doublon, réglage le plus récent gagne, ancienne sauvegarde)
- [ ] Voix : choix et débit conservés, voix naturelle téléchargée puis lue, retirée ; `sw.js` ne supprime que les caches `plrestart-`
- [ ] Clair et sombre, largeur téléphone et tablette ; contrastes du texte ≥ 4,5:1 et des contours de contrôle ≥ 3:1 (voir la charte)
