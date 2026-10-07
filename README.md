# PL-restart’

Prototype d'outil de classe : aider un élève à repartir quand il bloque sur une tâche scolaire. Page web unique, sans compte ni serveur : tout est enregistré localement dans le navigateur (aucune donnée n'est envoyée où que ce soit).

**Statut : prototype à tester.** Aucune preuve n'établit encore que cet outil aide. Il sert à le vérifier en classe ressource avec quelques élèves. Le dossier de conception détaille les choix, les sources et les limites.

## Ce que fait l'outil

L'élève choisit sa vignette, indique sa tâche, puis travaille. L'écran ne sert qu'au moment du blocage :

1. « Je suis bloqué »
2. « Où ça coince ? » : cinq familles (comprendre, commencer, décrocher, ça ne marche pas, je ne sais pas)
3. une ou deux questions de précision
4. une carte de stratégie : une seule action, une à dix minutes
5. « Ça repart ? » : oui, un peu, non
6. après trois « non », l'outil aide à formuler une demande d'aide précise, ou à mettre un « ? » dans la marge et à passer à la suite

Deux façons de l'utiliser :

- **Avec suivi** : l'élève choisit sa vignette et sa tâche ; blocages, cartes essayées et reprises sont enregistrés.
- **Juste un coup de main** : on arrive droit sur « Où ça coince ? », sans rien enregistrer (sauf rattachement à un élève). C'est aussi le mode à projeter pour montrer la démarche.

### Boîte à stratégies

Les quatorze stratégies du dossier de français « Ma boîte à stratégies » (comprendre une consigne, comprendre un texte, relire, planifier et écrire) sont intégrées avec leurs trois étapes et leur « truc ». Pour chaque élève, l'enseignante indique où il en est : pas encore vue (jamais proposée), guidée étape par étape, avec la carte, seul. Une stratégie connue passe avant la carte générique équivalente.

L'écran « Mon plan d'attaque » (stratégie 13) peut être activé par élève : au démarrage d'une tâche, l'élève choisit ses stratégies ; à la fin, il dit si son plan l'a aidé.

L'espace enseignante (roue dentée sur l'accueil) contient le suivi par élève, les niveaux d'aide, la boîte à stratégies, les libellés modifiables, l'export et l'effacement.

## Utiliser l'application

**En ligne** : publier ce dépôt via GitHub Pages et ouvrir l'adresse du site. Après une première visite, l'outil fonctionne sans réseau et peut être ajouté à l'écran d'accueil d'une tablette.

**En local** : ouvrir `index.html` dans un navigateur, en gardant le dossier `fonts/` à côté.

## Données et confidentialité

Les données (pseudonymes, tâches, blocages, cartes essayées, notes de l'enseignante) restent dans le navigateur de l'appareil (`localStorage`). Un autre appareil ou un autre navigateur repart de zéro. L'onglet « Données » permet d'exporter une sauvegarde `.json`, un tableau `.csv` des essais, d'importer une sauvegarde et de tout effacer.

L'outil n'enregistre ni nom complet, ni diagnostic, ni contenu de la tâche, ni texte libre de l'élève. Utilisez des pseudonymes. Même ainsi, ces données décrivent le fonctionnement de mineurs : vérifiez le cadre applicable dans votre établissement avant tout usage au-delà de votre classe.

## Limites connues

- La cause choisie par l'élève est une hypothèse, pas un diagnostic.
- L'outil ignore le contenu de la tâche : il ne vérifie ni la compréhension ni la justesse.
- Les seuils de niveau d'aide (5 reprises sur 6, 2 échecs de suite) sont des valeurs de départ sans base empirique.
- Le carnet affiche des comptes, sans classement.
- Le texte des stratégies du dossier n'est pas modifiable depuis l'espace enseignante ; les exemples de Léo ne sont pas repris.
- Non réalisés dans ce prototype : choix pondéré des cartes selon l'historique, niveau « sans l'outil », écran de bilan partagé élève et enseignante.

## Polices

Atkinson Hyperlegible et Lexend sont fournies dans `fonts/` (licence SIL OFL). Aucune requête vers un service tiers.

## Licence

Creative Commons Attribution – Pas d'utilisation commerciale 4.0 International (CC BY-NC 4.0). Voir [LICENSE](LICENSE).
