#  Projet Trafic Paris - Jalon M1

##  Description
Ce dépôt contient l'Analyse Exploratoire des Données (EDA) réalisée dans le cadre du Jalon M1. L'objectif est d'identifier et de valider le potentiel de 3 axes pilotes pour des aménagements de voirie.

##  Axes sélectionnés
La stratégie repose sur une combinaison d'un axe structurant et de deux "quick wins" (axes courts mais saturés) :
1. **Bd_Richard_Lenoir** : Axe structurant à fort volume (10 tronçons, débit ~95%).
2. **Quai_Conti** : Quick win en centre-ville (2 tronçons, saturation ~99%).
3. **Sts_Peres** : Quick win de liaison (4 tronçons, saturation ~99%).

##  Contenu du dépôt
- `Projet_Trafic.ipynb` : Notebook contenant le chargement des données par chunks, l'exploration (`.head()`, `.info()`) et les visualisations.
- `*.png` : Images des graphiques générés (débit, occupation, évolution horaire).
- `data/` : Dossier contenant les données brutes (ignoré par Git via `.gitignore` pour des raisons de poids).

##  Auteur
Fred Youaleu
