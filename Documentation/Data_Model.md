# Data Model — HR Attrition Analysis

## Présentation

Le modèle de données de ce projet a été conçu dans **Power BI** afin d'analyser l'attrition des employés sous différents angles : département, fonction, ancienneté, rémunération, satisfaction, niveau hiérarchique et conditions de travail.

L'objectif de la modélisation est de construire une structure claire et fiable permettant :

- de simplifier les analyses ;
- de limiter la redondance des informations ;
- de faciliter la création des mesures DAX ;
- d'améliorer la navigation entre les différentes dimensions d'analyse ;
- de garantir la cohérence des résultats du dashboard.

Le modèle repose principalement sur une **table de faits `Fact_Employee`** et deux **tables de dimensions** :

- `Dim_Department`
- `Dim_JobRole`

---

# Architecture du modèle

```text
                  ┌───────────────────────┐
                  │    Dim_Department     │
                  │───────────────────────│
                  │ DepartmentID          │
                  │ Department            │
                  └───────────┬───────────┘
                              │
                              │ 1
                              │
                              │ *
                  ┌───────────▼───────────┐
                  │    Fact_Employee      │
                  │───────────────────────│
                  │ EmployeeNumber        │
                  │ DepartmentID          │
                  │ JobRoleID             │
                  │ Attrition             │
                  │ MonthlyIncome         │
                  │ OverTime              │
                  │ JobLevel              │
                  │ JobSatisfaction       │
                  │ EnvironmentSatisfaction│
                  │ WorkLifeBalance       │
                  │ TotalWorkingYears     │
                  │ YearsAtCompany        │
                  │ ...                   │
                  └───────────▲───────────┘
                              │
                              │ *
                              │
                              │ 1
                  ┌───────────┴───────────┐
                  │      Dim_JobRole      │
                  │───────────────────────│
                  │ JobRoleID             │
                  │ JobRole               │
                  └───────────────────────┘
```

Le modèle suit une logique de **schéma en étoile (Star Schema)** simplifiée.

---

# 1. Fact_Employee

`Fact_Employee` constitue la table centrale du modèle.

Elle contient les informations nécessaires à l'analyse individuelle des employés.

### Volume

| Élément | Valeur |
|---|---:|
| Nombre de lignes | **1 470** |
| EmployeeNumber distincts | **1 470** |
| DepartmentID distincts | **3** |
| DepartmentID vides | **0** |
| JobRoleID distincts | **9** |
| JobRoleID vides | **0** |

Chaque ligne correspond à un employé.

L'identifiant `EmployeeNumber` permet d'identifier de manière unique chaque employé.

---

## Principales catégories de données

### Informations démographiques

- `Age`
- `Gender`
- `MaritalStatus`
- `Education`
- `EducationField`

### Informations professionnelles

- `DepartmentID`
- `JobRoleID`
- `JobLevel`
- `BusinessTravel`
- `JobInvolvement`
- `OverTime`

### Informations financières

- `DailyRate`
- `HourlyRate`
- `MonthlyIncome`
- `MonthlyRate`
- `PercentSalaryHike`
- `StockOptionLevel`

### Satisfaction et performance

- `EnvironmentSatisfaction`
- `JobSatisfaction`
- `RelationshipSatisfaction`
- `WorkLifeBalance`
- `PerformanceRating`

### Expérience et ancienneté

- `TotalWorkingYears`
- `YearsAtCompany`
- `YearsInCurrentRole`
- `YearsSinceLastPromotion`
- `YearsWithCurrManager`
- `NumCompaniesWorked`

### Variable d'analyse principale

- `Attrition`

Cette variable indique si l'employé a quitté l'entreprise :

- `Yes` → employé ayant quitté l'entreprise
- `No` → employé restant dans l'entreprise

---

# 2. Dim_Department

`Dim_Department` est une table de dimension permettant d'analyser les employés selon leur département.

Elle contient trois départements.

| DepartmentID | Department |
|---:|---|
| 1 | Sales |
| 2 | Research & Development |
| 3 | Human Resources |

### Contrôles

Après la fusion avec `Fact_Employee` :

- **1 470 lignes** conservées ;
- **3 valeurs distinctes** de `DepartmentID` ;
- **0 valeur nulle** de `DepartmentID`.

La correspondance entre les employés et leurs départements est donc complète.

---

# 3. Dim_JobRole

`Dim_JobRole` permet d'analyser l'attrition selon la fonction occupée par l'employé.

La dimension contient **9 fonctions**.

| JobRoleID | JobRole |
|---:|---|
| 1 | Healthcare Representative |
| 2 | Human Resources |
| 3 | Laboratory Technician |
| 4 | Manager |
| 5 | Manufacturing Director |
| 6 | Research Director |
| 7 | Research Scientist |
| 8 | Sales Executive |
| 9 | Sales Representative |

### Contrôles

Les contrôles effectués après la fusion ont confirmé :

| Contrôle | Résultat |
|---|---:|
| Lignes `Fact_Employee` | **1 470** |
| `JobRoleID` distincts | **9** |
| `JobRoleID` vides | **0** |
| Lignes `Dim_JobRole` | **9** |
| `JobRoleID` distincts dans la dimension | **9** |

La correspondance entre les employés et leurs fonctions est donc complète.

---

# Relations du modèle

Le modèle utilise deux relations principales.

## Relation 1 — Department

```text
Dim_Department[DepartmentID]
            │
            │ 1 : *
            ▼
Fact_Employee[DepartmentID]
```

Un département peut être associé à plusieurs employés.

---

## Relation 2 — Job Role

```text
Dim_JobRole[JobRoleID]
            │
            │ 1 : *
            ▼
Fact_Employee[JobRoleID]
```

Une fonction peut être associée à plusieurs employés.

---

# Pourquoi un Star Schema ?

La structure en étoile permet de séparer :

### Les informations descriptives

dans les dimensions :

```text
Dim_Department
Dim_JobRole
```

### Les données analytiques

dans :

```text
Fact_Employee
```

Cette organisation présente plusieurs avantages :

- modèle plus lisible ;
- relations simples ;
- réduction de la redondance ;
- meilleure organisation des analyses ;
- création de mesures DAX facilitée ;
- navigation plus intuitive dans Power BI.

---

# Contrôles de qualité du modèle

Plusieurs contrôles ont été réalisés afin de vérifier l'intégrité du modèle.

### Contrôle de l'effectif

```text
Fact_Employee = 1 470 lignes
```

### Contrôle de l'identifiant employé

```text
EmployeeNumber = 1 470 valeurs distinctes
```

L'identifiant est donc unique.

### Contrôle du département

```text
DepartmentID = 3 valeurs distinctes
Valeurs nulles = 0
```

### Contrôle des fonctions

```text
JobRoleID = 9 valeurs distinctes
Valeurs vides = 0
```

### Contrôles métier

Les contrôles de cohérence effectués sur les variables liées à l'expérience et à l'ancienneté ont donné :

| Contrôle | Incohérences |
|---|---:|
| Experience | **0** |
| Role | **0** |
| Manager | **0** |
| Promotion | **0** |

Ces contrôles permettent de confirmer que les relations et les données utilisées dans le modèle sont cohérentes avec les règles métier étudiées.

---

# Principes de modélisation appliqués

Les principaux principes utilisés dans ce projet sont :

### 1. Un identifiant unique

`EmployeeNumber` identifie chaque employé de manière unique.

### 2. Des dimensions séparées

Les informations répétitives relatives aux départements et aux fonctions ont été séparées dans des tables de dimensions.

### 3. Des clés de liaison

`DepartmentID` et `JobRoleID` servent de clés pour relier les dimensions à la table centrale.

### 4. Des relations 1:N

Les dimensions filtrent la table `Fact_Employee`.

### 5. Une table centrale orientée analyse

`Fact_Employee` concentre les informations nécessaires aux calculs et aux analyses DAX.

---

# Utilisation dans Power BI

La modélisation permet notamment de réaliser des analyses telles que :

```text
Department
     ↓
Taux d'attrition
```

```text
JobRole
     ↓
Taux d'attrition
```

```text
Department + OverTime
     ↓
Taux d'attrition
```

```text
JobRole + OverTime
     ↓
Taux d'attrition
```

```text
Ancienneté + OverTime
     ↓
Taux d'attrition
```

Cette structure permet donc de construire les différentes vues analytiques du dashboard tout en conservant une logique de modèle cohérente.

---

# Résumé du modèle

| Élément | Description |
|---|---|
| `Fact_Employee` | Table centrale contenant les données des employés |
| `Dim_Department` | Dimension des départements |
| `Dim_JobRole` | Dimension des fonctions |
| `EmployeeNumber` | Identifiant unique de l'employé |
| `DepartmentID` | Clé de liaison vers le département |
| `JobRoleID` | Clé de liaison vers la fonction |
| Architecture | **Star Schema simplifié** |
| Employés | **1 470** |
| Départements | **3** |
| Fonctions | **9** |

---

## Conclusion

Le modèle de données a été conçu pour fournir une base fiable et structurée à l'analyse de l'attrition.

La séparation entre `Fact_Employee`, `Dim_Department` et `Dim_JobRole` permet de faciliter les analyses multidimensionnelles tout en conservant un modèle simple, lisible et adapté aux besoins du dashboard.

Les différents contrôles réalisés ont permis de vérifier l'intégrité des clés, les cardinalités attendues et la cohérence des données avant la création des indicateurs DAX.

**Technologies :** Power BI · Power Query · DAX · Data Modeling