# HR Attrition Analysis — Power BI

> Analyse interactive de l'attrition des employés à l'aide de Power BI, Power Query et DAX.

---

##  Présentation du projet

Ce projet consiste à analyser l'attrition des employés afin d'identifier les populations présentant les niveaux de départ les plus élevés et de mettre en évidence les facteurs associés à l'attrition.

L'objectif est de transformer des données RH brutes en un outil d'analyse interactif permettant aux équipes RH et aux managers d'explorer les tendances d'attrition et d'identifier les populations nécessitant une attention particulière.

Le projet couvre l'ensemble du processus analytique :

**Data Preparation → Data Modeling → DAX → Data Visualization → Business Insights**

---

##  Problématique métier

**Quels facteurs sont associés au départ des employés et quelles populations présentent les niveaux d'attrition les plus élevés ?**

L'analyse cherche notamment à répondre aux questions suivantes :

- Le taux d'attrition varie-t-il selon le département ?
- Quel est le lien entre l'OverTime et l'attrition ?
- Certaines fonctions sont-elles davantage exposées ?
- L'ancienneté est-elle associée à des niveaux d'attrition différents ?
- La satisfaction au travail est-elle associée au départ des employés ?
- Certaines combinaisons de facteurs permettent-elles d'identifier des populations particulièrement exposées ?

---

##  Dataset

**Source :** IBM HR Analytics Employee Attrition & Performance

Le dataset contient des informations relatives à :

- l'âge ;
- la rémunération ;
- l'ancienneté ;
- le département ;
- la fonction ;
- le niveau hiérarchique ;
- la satisfaction ;
- l'OverTime ;
- la performance ;
- l'attrition.

### Volume

| Élément | Valeur |
|---|---:|
| Employés | **1 470** |
| Employés ayant quitté l'entreprise | **237** |
| Employés restants | **1 233** |
| Taux d'attrition global | **16,12 %** |

---

##  Objectifs de l'analyse

### 1. Mesurer

Construire les principaux indicateurs liés à l'effectif et à l'attrition.

### 2. Identifier

Identifier les départements, fonctions et populations présentant les taux d'attrition les plus élevés.

### 3. Explorer

Analyser les relations entre l'attrition et :

- OverTime ;
- Department ;
- JobRole ;
- JobLevel ;
- JobSatisfaction ;
- EnvironmentSatisfaction ;
- WorkLifeBalance ;
- ancienneté.

### 4. Restituer

Construire un dashboard Power BI interactif permettant d'explorer les résultats.

---

##  Modèle de données

Le modèle analytique repose sur une table centrale `Fact_Employee` associée à deux dimensions principales :

                 Dim_Department
                       │
                       │
                       ▼
                 Fact_Employee
                       ▲
                       │
                       │
                   Dim_JobRole


### Fact_Employee

Table centrale contenant les informations individuelles des employés et les variables utilisées pour l'analyse.

### Dim_Department

Dimension contenant les trois départements :

| DepartmentID | Department |
|---:|---|
| 1 | Sales |
| 2 | Research & Development |
| 3 | Human Resources |

### Dim_JobRole

Dimension contenant les **9 fonctions** présentes dans le dataset.

### Contrôles effectués

- `EmployeeNumber` : **1 470 valeurs distinctes**
- `DepartmentID` : **3 valeurs distinctes, 0 valeur nulle**
- `JobRoleID` : **9 valeurs distinctes, 0 valeur vide**
- `Fact_Employee` : **1 470 lignes**

[Voir la documentation du modèle](Documentation/Data_Model.md)

---

## Préparation des données

La préparation des données a été réalisée avec **Power Query**.

Les principales étapes ont été :

- contrôle du nombre de lignes et de colonnes ;
- vérification des types de données ;
- analyse des valeurs distinctes ;
- contrôle des valeurs manquantes ;
- identification des variables constantes ;
- contrôle de l'unicité des identifiants ;
- création de `DepartmentID` ;
- création de `JobRoleID` ;
- contrôle de la cohérence des relations entre les variables d'ancienneté et d'expérience.

### Variables constantes identifiées

Les variables suivantes ne présentent qu'une seule valeur distincte :

- `EmployeeCount`
- `StandardHours`
- `Over18`

Elles présentent donc une faible valeur analytique dans ce contexte.

### Contrôles de cohérence

| Contrôle | Résultat |
|---|---:|
| Experience | **0 incohérence** |
| Role | **0 incohérence** |
| Manager | **0 incohérence** |
| Promotion | **0 incohérence** |

[Voir la documentation Power Query](Documentation/Power_Query.md)

---

##  Mesures DAX

Plusieurs mesures DAX ont été développées afin de construire les KPI et les analyses du dashboard.

### Nombre d'employés

Nombre Employés =
DISTINCTCOUNT(
    Fact_Employee[EmployeeNumber]
)


### Employés ayant quitté l'entreprise

Employés Ayant Quitté =
CALCULATE(
    [Nombre Employés],
    Fact_Employee[Attrition] = "Yes"
)


### Taux d'attrition

Taux Attrition =
DIVIDE(
    [Employés Ayant Quitté],
    [Nombre Employés],
    0
)


### Revenu mensuel moyen

Revenu Mensuel Moyen =
AVERAGE(
    Fact_Employee[MonthlyIncome]
)


D'autres mesures ont été développées pour analyser la rémunération, l'attrition par population et les écarts entre différents groupes.

[Voir la documentation DAX](Documentation/DAX_Measures.md)

---

## Dashboard

Le rapport Power BI est organisé en trois pages principales.

### 01 — Executive Overview

Vue synthétique permettant de suivre les principaux KPI et d'explorer l'attrition selon différentes dimensions.

![Executive Overview](Screenshots/Executive_Overview.png)

---

### 02 — Attrition Analysis

Analyse approfondie du taux d'attrition selon notamment :

- Department ;
- OverTime ;
- JobRole ;
- JobLevel ;
- satisfaction ;
- ancienneté.

[Attrition Analysis](Screenshots/Attrition_Analysis.png)

---

### 03 — HR Insights

Page dédiée à la synthèse des principaux enseignements de l'analyse et aux pistes d'action potentielles.

![HR Insights](Screenshots/HR_Insights.png)

---

## Key Insights

### 1. L'OverTime est fortement associé à l'attrition

Le taux d'attrition atteint :

**30,53 %**

chez les employés travaillant en OverTime, contre :

**10,44 %**

chez ceux qui n'en font pas.

L'écart observé est de :

**20,09 points de pourcentage.**

---

### 2. Les employés en début d'ancienneté présentent une attrition élevée

Le groupe **Début** présente un taux d'attrition de :

**29,82 %**

Lorsque l'on combine :

**Début + OverTime**

le taux atteint :

**50,96 %.**

Cette population constitue donc un groupe particulièrement intéressant à surveiller.

---

### 3. Les Sales Representatives constituent une population fortement exposée

Le taux d'attrition global des **Sales Representatives** est de :

**39,76 %.**

Chez les Sales Representatives travaillant en OverTime, il atteint :

**66,67 %.**

---

### 4. La satisfaction est associée à des niveaux d'attrition différents

Pour `JobSatisfaction` :

| Niveau | Taux d'attrition |
|---:|---:|
| 1 | **22,84 %** |
| 2 | **16,43 %** |
| 3 | **16,52 %** |
| 4 | **11,33 %** |

Le niveau de satisfaction le plus faible présente donc un taux d'attrition sensiblement supérieur au niveau de satisfaction le plus élevé.

---

## Recommandations

Sur la base des associations observées dans les données, plusieurs pistes peuvent être envisagées :

### 1. Surveiller l'OverTime

Identifier les équipes et fonctions où l'OverTime est particulièrement fréquent et analyser la charge de travail.

### 2. Renforcer l'accompagnement des nouveaux employés

Mettre en place un suivi renforcé pendant les premières années de présence dans l'entreprise.

### 3. Examiner la situation des Sales Representatives

Analyser leurs objectifs, leur charge de travail, leur rémunération et leurs conditions de travail.

### 4. Suivre les niveaux de satisfaction

Porter une attention particulière aux populations présentant les niveaux de satisfaction les plus faibles.

---

## Limites de l'analyse

Les résultats présentés dans ce projet mettent en évidence des **associations observées dans les données**.

Ils ne permettent pas, à eux seuls, d'établir une relation de causalité.

Par exemple, l'analyse montre une forte association entre **OverTime** et **Attrition**, mais elle ne permet pas d'affirmer que l'OverTime est directement la cause des départs.

Une analyse causale nécessiterait des données supplémentaires et une méthodologie statistique adaptée.

---

## Perspectives d'amélioration

Le projet pourrait être enrichi par :

- l'intégration de données historiques ;
- l'analyse temporelle de l'attrition ;
- l'intégration de données RH supplémentaires ;
- une analyse statistique plus approfondie ;
- la construction d'un modèle prédictif avec Python ;
- la création d'un score de risque d'attrition ;
- l'automatisation de l'actualisation des données.

Une prochaine évolution pourrait notamment consister à utiliser **Python et le Machine Learning** pour construire un modèle permettant d'identifier les employés présentant un risque potentiel de départ.

---

## Technologies utilisées

- **Power BI**
- **Power Query / M**
- **DAX**
- **Data Modeling**
- **Data Visualization**
- **Git**
- **GitHub**

---

## Compétences démontrées

- Data Cleaning
- Data Transformation
- Data Modeling
- Star Schema
- DAX
- KPI Development
- Data Visualization
- Interactive Dashboard Design
- Business Analysis
- Data Storytelling
- Git / GitHub

---

## Structure du projet

hr-attrition-analysis-powerbi/
│
├── PowerBI/
│   └── HR_Attrition_Analysis.pbix
│
├── Documentation/
│   ├── Data_Model.md
│   ├── Power_Query.md
│   └── DAX_Measures.md
│
├── Screenshots/
│   ├── Executive_Overview.png
│   ├── Attrition_Analysis.png
│   └── HR_Insights.png
│
├── Data/
│   └── README.md
│
├── .gitignore
└── README.md

---

##  Auteur

**Babou Diouf**

Data Analyst — Power BI | SQL | DAX | Power Query

---

> **Projet réalisé dans une démarche de portfolio professionnel Data Analyst.**