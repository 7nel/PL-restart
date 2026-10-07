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
6. après trois « non », l'outil aide à formuler une demande d'aide précise

L'espace enseignante (roue dentée sur l'accueil) contient le suivi par élève, les niveaux d'aide, les libellés modifiables, l'export et l'effacement.

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
- Non réalisés dans ce prototype : choix pondéré des cartes selon l'historique, niveau « sans l'outil », écran de bilan partagé élève et enseignante.

## Polices

Atkinson Hyperlegible et Lexend sont fournies dans `fonts/` (licence SIL OFL). Aucune requête vers un service tiers.

## Licence

Creative Commons Attribution – Pas d'utilisation commerciale 4.0 International (CC BY-NC 4.0). Voir [LICENSE](LICENSE).
