# PL-restart’

Prototype d'outil de classe : aider un élève à repartir quand il bloque sur une tâche scolaire. Page web unique, sans compte ni serveur : tout est enregistré localement dans le navigateur (aucune donnée n'est envoyée où que ce soit).

**Statut : prototype à tester.** Aucune preuve n'établit encore que cet outil aide. Il sert à le vérifier en classe ressource avec quelques élèves. Le dossier de conception détaille les choix, les sources et les limites.

## Ce que fait l'outil

L'élève choisit sa vignette, indique sa tâche et la matière, puis (deuxième écran, déjà rempli comme la dernière fois) le lieu et si un adulte peut l'aider, puis travaille. L'écran ne sert qu'au moment du blocage :

1. « Je suis bloqué »
2. « Où ça coince ? » : cinq familles (comprendre, commencer, décrocher, ça ne marche pas, je ne sais pas), et, à part, « Ça ne va pas du tout »
3. une ou deux questions de précision, quatre réponses visibles au plus (« Autre chose » ouvre les suivantes ; « Étape précédente » revient d'un pas) et, si elle change la stratégie, la matière, la tâche ou le lieu
4. une stratégie : une à trois étapes, une à douze minutes
5. « Ça repart ? » : oui, un peu, non. Après un « non », une phrase courte reconnaît l'effort (« Pas grave. On essaie autre chose. ») ; quand ça repart, l'écran reprend le nom de la stratégie qui a aidé. Ni points ni récompenses.
6. après trois « non », l'outil aide à formuler une demande d'aide précise, ou à mettre un « ? » dans la marge et à passer à la suite

### Adulte disponible ou non

L'élève l'indique au démarrage et peut le changer sur l'écran de travail. S'il ne l'a pas dit, l'outil le demande au moment de l'aide (c'est toujours le cas en mode coup de main). Sans adulte disponible, l'outil propose une stratégie de plus, puis l'élève prépare sa question pour plus tard et met un « ? » dans la marge. Le suivi indique quels blocages ont eu lieu sans adulte disponible.

Deux façons de l'utiliser :

- **Avec suivi** : l'élève choisit sa vignette et sa tâche ; blocages, stratégies essayées et reprises sont enregistrés.
- **Juste un coup de main** : on arrive droit sur « Où ça coince ? », sans rien enregistrer (sauf rattachement à un élève). C'est le mode à projeter pour montrer la démarche, et celui qui convient à la maison : chaque appareil garde ses propres données, l'enseignante ne voit pas ce qui s'y passe.

### Stratégies

Cinq domaines, plus les cartes générales :

- **Dossier de français** « Ma boîte à stratégies » : quatorze stratégies (consigne, lecture, relecture, écriture), avec leurs trois étapes et leur « truc ».
- **Maths** (7), **Réviser et mémoriser** (6), **Langues** (6), **Fatigue et stress** (6) : stratégies en trois étapes, rédigées et validées par l'enseignante.

L'outil choisit en croisant le blocage, la tâche, la matière et le lieu. Certaines réponses n'apparaissent que dans leur contexte (« Je bloque sur un calcul » en maths, « Ce que j'entends » en langues).

### Retrait de l'aide

Chaque stratégie a quatre états par élève : pas encore vue (jamais proposée), guidée étape par étape, avec la carte, seul (le nom seulement, carte à la demande). Le dossier de français commence « pas encore vu », le reste « guidé ».

L'outil propose, l'enseignante valide : après trois reprises de suite avec une stratégie, l'onglet « Suivi » suggère d'alléger ; après deux échecs de suite, ou deux cartes rouvertes au niveau « seul », il suggère de renforcer. Rien ne change sans validation.

### Plan d'attaque et vérification

Deux écrans s'activent par élève : « Mon plan d'attaque » au démarrage d'une tâche (quatre stratégies au plus selon la tâche et la matière, le reste derrière « Voir toutes mes stratégies ») et « Avant de rendre » quand la tâche est terminée (trois points à vérifier).

L'espace enseignante (roue dentée sur l'accueil) contient le suivi par élève, les suggestions de retrait de l'aide, l'état de chaque stratégie, les libellés modifiables, l'export et l'effacement.

## Utiliser l'application

**En ligne** : publier ce dépôt via GitHub Pages et ouvrir l'adresse du site. Après une première visite, l'outil fonctionne sans réseau et peut être ajouté à l'écran d'accueil d'une tablette.

**En local** : ouvrir `index.html` dans un navigateur, en gardant le dossier `fonts/` à côté.

## Données et confidentialité

Les données (pseudonymes, tâches, blocages, cartes essayées, notes de l'enseignante) restent dans le navigateur de l'appareil (`localStorage`). Un autre appareil ou un autre navigateur repart de zéro. L'onglet « Données » permet :

- d'**exporter un élève** (un fichier `.json` : fiche, réglages, tâches, blocages), une sauvegarde complète, ou un tableau `.csv` des essais ;
- d'**importer et fusionner** un ou plusieurs fichiers, sans rien effacer : un élève est reconnu par son identifiant interne (option : même prénom) ; ses réglages, la version réglée en dernier l'emporte ; tâches et blocages sont ajoutés s'ils manquent, notes de l'enseignante conservées ; libellés des cartes complétés seulement ;
- de tout effacer (pour repartir d'une sauvegarde).

Le code de l'espace enseignante (lettres et chiffres) est gardé en clair dans le navigateur de l'appareil, n'est jamais écrit dans un fichier exporté ni repris à l'import. Les sauvegardes faites avant la version 5 le contiennent. Il évite qu'un élève entre par curiosité ; ce n'est pas une protection des données. Tant qu'aucun code n'est défini et qu'il y a des élèves, l'espace enseignante affiche un rappel en haut.

Les transferts se font à la main (clé, messagerie de l'école) : l'outil n'envoie rien.

L'outil n'enregistre ni nom complet, ni diagnostic, ni contenu de la tâche, ni texte libre de l'élève. Utilisez des pseudonymes. Même ainsi, ces données décrivent le fonctionnement de mineurs : vérifiez le cadre applicable dans votre établissement avant tout usage au-delà de votre classe.

## Limites connues

- La cause choisie par l'élève est une hypothèse, pas un diagnostic.
- L'outil ignore le contenu de la tâche : il ne vérifie ni la compréhension ni la justesse.
- Les seuils des suggestions (3 reprises de suite, 2 échecs de suite) sont des valeurs de départ sans base empirique. Les « reprises » sont déclarées par l'élève.
- Le retrait progressif de l'aide n'a pas montré d'avantage net dans les méta-analyses sur l'étayage informatisé : c'est un choix pédagogique, à observer.
- La stratégie de plus proposée sans adulte (suite de la ligne, sinon une carte générale par famille) et les quatre stratégies montrées d'abord dans le plan d'attaque sont des choix de conception, à ajuster à l'usage.
- À la maison, rien ne remonte à l'enseignante : pas de suivi partagé sans serveur.
- En mode coup de main, la réponse « Je crois savoir, mais je rate » n'apparaît que si la tâche « Réviser » est déjà connue.
- Le carnet affiche des comptes, sans classement.
- Le texte des stratégies (dossier et quatre domaines) n'est pas modifiable depuis l'espace enseignante ; les exemples de Léo ne sont pas repris.
- La voix naturelle a été testée dans un navigateur automatisé (génération et lecture) ; sa vitesse et son fonctionnement sur tablettes d'école ne sont pas vérifiés.
- Non réalisés dans ce prototype : choix pondéré des cartes selon l'historique, niveau « sans l'outil », écran de bilan partagé élève et enseignante.

## Voix

Le bouton « Écouter » utilise la meilleure voix française du système (classement repris de PL-lect’), réglable dans « Réglages » (voix, débit, test). Sur la version en ligne (https), on peut aussi télécharger une **voix naturelle** (Piper, environ 93 Mo, une fois par appareil) : elle fonctionne ensuite hors ligne. Ses fichiers sont dans `voix/`, servis par le même site et gardés par `sw.js` ; aucun service tiers. Depuis un fichier ouvert directement, seule la voix du système est disponible. Licences : voir [NOTICE.md](NOTICE.md).

## Polices

Atkinson Hyperlegible et Lexend sont fournies dans `fonts/` (licence SIL OFL). Aucune requête vers un service tiers.

## Licence

Creative Commons Attribution – Pas d'utilisation commerciale 4.0 International (CC BY-NC 4.0). Voir [LICENSE](LICENSE).
