# Analyse du Trafic Routier a Paris : 3 Axes Structurants

**Projet de Data Science - Aivancity**
**Auteur :** Fred Youaleu
**Depot :** [github.com/Fred-Youaleu/Projet_Trafic](https://github.com/Fred-Youaleu/Projet_Trafic)

---

## Contexte et Problematique

La ville de Paris dispose de centaines de capteurs de trafic permanents qui mesurent en continu le debit et le taux d'occupation des axes routiers. Ce projet vise a **comprendre la dynamique du trafic** sur trois axes parisiens aux profils tres differents :

- **Boulevard Richard Lenoir** (11eme) - Axe structurant nord-sud
- **Quai Conti** (6eme) - Axe touristique et commercial en bord de Seine
- **Rue des Saints-Peres** (6eme/7eme) - Axe de liaison inter-arrondissements

**Question centrale :** *Comment evoluent les profils de congestion et de debit sur ces trois axes, et dans quelle mesure les etats declares par les capteurs refletent-ils la realite physique du trafic ?*

---

## Les Donnees

| Caracteristique | Detail |
|-----------------|--------|
| **Source** | OpenData Paris - Comptages routiers permanents |
| **Periode** | Donnees horaires sur plusieurs mois (2026) |
| **Volume** | 69 824 mesures |
| **Variables cles** | `q` (debit veh/h), `k` (occupation %), `etat_trafic`, `etat_barre`, `t_1h` (heure Paris) |
| **Capteurs** | 16 capteurs repartis sur les 3 axes |

---

## Methodologie

Le projet s'est deroule en **4 etapes** successives, chacune correspondant a un notebook dedie :

### Jalon M1 - Chargement et Premiere Exploration (`projet_Trafic.ipynb`)
- Chargement du CSV brut et identification des colonnes.
- Detection des premieres incoherences (etats "Inconnu", "Invalide", debits aberrants).
- Premieres visualisations pour comprendre la structure et la qualite des donnees brutes.

### Jalon M2 - Nettoyage et Preparation (`Rendu 2.ipynb`)
- **Formatage des types** : `t_1h` en datetime (fuseau Paris), etats en categorie.
- **Verification des doublons** : detection de capteurs redondants (ex: capteurs 69 et 70 du Quai Conti).
- **Traitement des valeurs manquantes** :
  - Identification des etats "Inconnu"/"Invalide" comme incertitudes de classification.
  - Interpolation lineaire pour les trous courts (<= 3h, soit 99.4% des cas).
  - Conservation des NaN pour les trous longs (> 3h) pour eviter les biais.
- **Limites physiques** : plafonnement de `k` entre 0 et 100%, gestion des debits > 2000 veh/h.

### Jalon M3 - Analyse Exploratoire (`Rendu 3.ipynb`)
- **Variables temporelles** : extraction de l'heure, jour de semaine, indicateur week-end.
- **Encodage** :
  - `etat_trafic` -> encodage ordinal (Fluide=0, Pre-sature=1, Sature=2, Bloque=3) apres validation de l'ordre par la mediane de `k`.
  - `etat_barre` et `libelle` -> one-hot encoding.
- **Valeurs aberrantes** : comparaison IQR vs limites physiques -> decision de garder les valeurs physiquement plausibles.
- **Statistiques descriptives** par troncon avec interpretation metier.

### Rendu Final  - Visualisations Strategiques (`Rendu Final 4.ipynb`)
- **Profil horaire** : comparaison Semaine vs Week-end pour mettre en evidence l'impact du mode de vie sur le trafic.
- **Heatmap de la congestion** : cartographie visuelle des periodes de tension maximale (jour x heure).
- **Diagramme Fondamental** : relation physique entre debit et occupation, avec trajectoire des medianes par etat de trafic.
- **Analyse des seuils de basculement** : determination des taux d'occupation critiques pour chaque etat et chaque axe.

---

## Resultats

### Phase 1 : Exploration initiale des donnees brutes (Jalon M1)
Avant tout nettoyage, les premieres visualisations ont permis d'identifier la structure globale et les anomalies majeures du jeu de donnees.

**1. Distribution brute du debit**
![Debit brut](images/graph1_debit.png)
Le debit brut presente une forte dispersion avec des valeurs extremes, necessitant une verification des limites physiques des capteurs.

**2. Distribution brute de l'occupation**
![Occupation brute](images/graph2_occupation.png)
Le taux d'occupation (`k`) montre egalement des valeurs incoherentes (superieures a 100% ou negatives) qui seront corrigees lors du nettoyage.

**3. Profil horaire global**
![Horaire brut](images/graph3_horaire.png)
Meme sur les donnees brutes, une cyclicite journaliere est deja visible, confirmant la pertinence d'une analyse temporelle approfondie.

### Phase 2 : Distributions apres nettoyage (Jalon M2 / M3)
Apres l'application des regles de nettoyage (interpolation, plafonnement de `k`, suppression des valeurs physiquement impossibles), les distributions sont assainies.

**4. Distribution du debit et de l'occupation par axe**
![Distribution finale](images/distribution_debit_occupation.png)
- **Bd Richard Lenoir** : mediane ~204 veh/h, max 828 veh/h - axe a fort volume mais stable.
- **Quai Conti** : mediane ~1032 veh/h, max 1563 veh/h - trafic dense avec pics marques.
- **Sts Peres** : mediane ~240 veh/h, max 1001 veh/h - variabilite importante.

### Phase 3 : Dynamiques temporelles et congestion (Rendu Final)

**5. Profil horaire : Semaine vs Week-end**
![Profil horaire](images/profil_horaire_semaine_weekend.png)
- **Effet week-end** : les pics de 9h et 18h disparaissent totalement le samedi et dimanche, confirmant un trafic a **dominante professionnelle**.
- **Comportement des axes** : le Bd Richard Lenoir subit la chute la plus brutale (axe de transit pur), tandis que le Quai Conti maintient un debit plus stable (tourisme/commerce).

**6. Heatmap de la congestion**
![Heatmap](images/heatmap_congestion.png)
La congestion (taux d'occupation `k`) est maximale **du lundi au vendredi, entre 8h-10h et 17h-19h**. Le week-end, la carte s'eclaircit drastiquement, validant l'absence de saturation structurelle hors jours ouvres.

### Phase 4 : Physique du trafic et seuils (Rendu Final)

**7. Diagramme Fondamental : Debit vs Occupation**
![Diagramme fondamental](images/diagramme_fondamental.png)
La trajectoire des medianes (ligne noire) revele la physique du trafic :
- De *Fluide* a *Pre-sature* : le debit augmente avec l'occupation.
- A partir de *Sature* : le debit stagne puis chute malgre une occupation croissante.
- *Bloque* : occupation tres elevee (> 50%), debit effondre.

**8. Seuils de basculement par axe**
![Seuils](images/seuils_basculement.png)

| Etat | Bd Richard Lenoir | Quai Conti | Sts Peres |
|------|-------------------|------------|-----------|
| Fluide | 1.59% | 8.47% | 4.46% |
| Pre-sature | 17.53% | 18.04% | 20.41% |
| Sature | 36.27% | 35.04% | 35.87% |
| Bloque | 52.22% | **0.00%** | 55.56% |

**Constat cle** : les seuils sont remarquablement stables d'un axe a l'autre (~17-20% pour Pre-sature, ~35-36% pour Sature), validant l'homogeneite de la physique du trafic. L'anomalie du Quai Conti (0% pour "Bloque") revele une **defaillance de l'etiquetage automatique** des capteurs.

---

## Limites

- **Trous de mesure** : les etats "Inconnu" (souvent la nuit) ont ete interpolés. Les trous > 3h restent en `NaN`.
- **Redondance de capteurs** : les capteurs 69 et 70 du Quai Conti renvoient des mesures strictement identiques (duplication probable au niveau de la collecte).
- **Etiquetage defaillant** : certains etats declares (ex: "Bloque" avec 0% d'occupation) contredisent la realite physique -> prudence requise.
- **Absence de typologie** : les donnees ne distinguent pas les types de vehicules (2 roues vs 4 roues).

---

## Lancer le projet

```bash
# 1. Cloner le depot
git clone https://github.com/Fred-Youaleu/Projet_Trafic.git
cd Projet_Trafic

# 2. Installer les dependances
pip install -r requirements.txt

# 3. Executer les notebooks dans l'ordre chronologique :
#    - projet_Trafic.ipynb : Chargement et premiere exploration (M1)
#    - Rendu 2.ipynb       : Nettoyage et preparation des donnees (M2)
#    - Rendu 3.ipynb       : Analyse exploratoire et visualisations (M3 + Rendu Final 4)