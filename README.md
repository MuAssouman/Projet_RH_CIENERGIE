
# 📊 Projet RH – CI-Energies
**Analyse de l'absentéisme de CI-Energies**

## 🎯 Objectif
Mettre en place un tableau de bord interactif Power BI pour analyser et surveiller l’absentéisme du personnel de CI-Energies(filiale RH de Côte d’Ivoire Energies).  
L’analyse vise à :

- Identifier les départements les plus touchés par l’absentéisme  
- Visualiser les types d’absences les plus fréquents  
- Suivre l’évolution des heures d’absence par an  
- Repérer les cas à risque (absences excessives hors congé)


## 🧱 Modèle de données  
Le modèle suit une **architecture en étoile**, avec une table de faits centrale et plusieurs dimensions :

### ✅ Table de faits
- `Fait_Absences` : issue du fichier `Absences.csv`

### ✅ Tables de dimensions
- `Dim_Employes` (depuis `Employes.csv`)
- `Dim_Departements` (depuis `Departements.csv`)
- `Dim_Sites` (depuis `Sites.csv`)
- `Dim_Type_Absence` (extrait des types uniques dans la table des faits)
- `Dim_Date` : calendrier dynamique généré dans Power BI avec DAX



## 📐 Mesures DAX principales

```DAX
-- Heure d’absence maximale sur la période
Max_h_absences = MAX(Fait_Absences[_heures_absences_ok])

-- Nombre d’absences enregistrées (tous types confondus)
nb_h_absence = COUNT(Fait_Absences[ID_Employe])

-- Taux d’absence par poste (base 173,2h mensuelles par employé)
Taux_Absence_Par_Poste = 
DIVIDE(
    CALCULATE(SUM(Fait_Absences[_heures_absences_ok])),
    CALCULATE(COUNTROWS(Dim_Employes)) * 173.2
)

-- Total d’heures d’absence hors congés
Total_HAbs_Hors_Conge = 
CALCULATE(
    SUM(Fait_Absences[_heures_absences_ok]),
    Fait_Absences[Type_Absence] <> "Conge"
)

-- Total général des heures d’absence
Total_Heures_Absence = SUM(Fait_Absences[_heures_absences_ok])


## 🎛️ Filtres et interactions Power BI

- **Segments** :
  - `Type_Absence`
  - `Année` (Dim_Date)
  - `Département` (Dim_Departements)

- **Navigation (Pages) :
  - Vue Globale
  - Analyse par Type d’Absence
  - Page Alerte RH

- **Mise en forme conditionnelle** :
  - Une alerte visuelle couleur bleu est déclenchée si un employé dépasse 90h d’absence par an (hors congés)


## 🎨 Design visuel

- Utilisation du thème Accessible Tidal
- Visualisations clés :
  - Histogramme empilé
  - Graphique en secteur
  - Carte et carte nouvelle
  - Graphique en courbe
