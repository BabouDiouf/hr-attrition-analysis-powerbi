# Power Query — HR Attrition Analysis

## Présentation

**Power Query** a été utilisé pour préparer, contrôler et structurer les données du projet **HR Attrition Analysis** avant leur exploitation dans le modèle Power BI.

L'objectif de cette étape était de s'assurer que les données étaient suffisamment propres, cohérentes et structurées pour permettre la construction des indicateurs DAX et du dashboard.

Le processus de préparation a principalement porté sur :

- le contrôle de la structure du dataset ;
- la vérification des types de données ;
- l'analyse de la qualité des données ;
- l'identification des variables constantes ;
- le contrôle des identifiants ;
- la création des clés de dimension ;
- les contrôles de cohérence métier ;
- la préparation de `Fact_Employee`.

---

# 1. Structure initiale des données

Le dataset contient :

- **1 470 lignes**
- **35 colonnes initiales**

Les variables sont de différents types :

- entiers ;
- textes ;
- variables catégorielles ;
- variables binaires (`Yes` / `No`) ;
- variables ordinales représentées par des valeurs numériques.

### Principales variables

| Catégorie | Variables |
|---|---|
| Démographie | `Age`, `Gender`, `MaritalStatus`, `Education`, `EducationField` |
| Organisation | `Department`, `JobRole`, `JobLevel`, `BusinessTravel` |
| Rémunération | `DailyRate`, `HourlyRate`, `MonthlyIncome`, `MonthlyRate` |
| Satisfaction | `EnvironmentSatisfaction`, `JobSatisfaction`, `RelationshipSatisfaction` |
| Performance | `PerformanceRating`, `PercentSalaryHike` |
| Ancienneté | `TotalWorkingYears`, `YearsAtCompany`, `YearsInCurrentRole` |
| Conditions de travail | `OverTime`, `WorkLifeBalance` |
| Attrition | `Attrition` |

---

# 2. Vérification des types de données

La première étape a consisté à contrôler le type de chaque colonne afin de s'assurer qu'il correspondait à son utilisation analytique.

### Exemples

Les variables quantitatives ont été conservées en type entier :

```text
Age
DailyRate
DistanceFromHome
HourlyRate
MonthlyIncome
MonthlyRate
TotalWorkingYears
YearsAtCompany
```

Les variables catégorielles ont été conservées en texte :

```text
Attrition
BusinessTravel
Department
EducationField
Gender
JobRole
MaritalStatus
OverTime
```

Les variables ordinales telles que `Education`, `JobLevel`, `JobSatisfaction` ou `WorkLifeBalance` ont été conservées sous forme numérique afin de permettre leur utilisation dans les analyses.

---

# 3. Contrôle de la qualité des données

Une vérification de la qualité des colonnes a été effectuée dans Power Query.

### Résultat

**Toutes les colonnes contrôlées présentent une qualité de données de 100 %.**

Aucune anomalie de validité n'a été identifiée lors de ce contrôle.

---

# 4. Analyse des valeurs distinctes

L'analyse des valeurs distinctes a permis d'identifier les variables présentant une faible valeur analytique.

## `EmployeeCount`

Nombre de valeurs distinctes :

```text
1
```

La variable est donc constante dans l'ensemble du dataset.

---

## `StandardHours`

Nombre de valeurs distinctes :

```text
1
```

Cette variable est également constante.

---

## `Over18`

Nombre de valeurs distinctes :

```text
1
```

La variable ne présente donc aucune variation dans le dataset.

---

## Conclusion

Ces trois variables ont été identifiées comme des variables constantes et ne permettent pas de différencier les employés dans l'analyse.

---

# 5. Contrôle de l'identifiant employé

La colonne :

```text
EmployeeNumber
```

a fait l'objet d'un contrôle d'unicité.

### Résultat

| Élément | Valeur |
|---|---:|
| Nombre de lignes | **1 470** |
| EmployeeNumber distincts | **1 470** |

Chaque employé possède donc un identifiant unique.

Cette vérification est importante car `EmployeeNumber` est utilisé pour compter les employés de manière fiable dans les mesures DAX.

---

# 6. Création de DepartmentID

Afin de mieux structurer le modèle de données, une clé numérique `DepartmentID` a été créée pour identifier les différents départements.

La correspondance retenue est :

| DepartmentID | Department |
|---:|---|
| 1 | Sales |
| 2 | Research & Development |
| 3 | Human Resources |

Cette clé permet de relier `Fact_Employee` à `Dim_Department`.

---

# 7. Vérification de DepartmentID

Après la fusion permettant d'intégrer `DepartmentID` dans `Fact_Employee`, plusieurs contrôles ont été effectués.

### Résultats

```text
Nombre de lignes : 1 470
DepartmentID distincts : 3
DepartmentID nulls : 0
```

La fusion n'a donc entraîné aucune perte de ligne et aucune correspondance manquante.

---

# 8. Création de JobRoleID

Une clé `JobRoleID` a également été créée afin d'identifier les différentes fonctions présentes dans les données.

Les 9 fonctions sont :

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

Cette clé permet de relier `Fact_Employee` à `Dim_JobRole`.

---

# 9. Vérification de JobRoleID

Après la fusion avec la dimension des fonctions, les contrôles suivants ont été effectués :

| Contrôle | Résultat |
|---|---:|
| Lignes `Fact_Employee` | **1 470** |
| `JobRoleID` distincts | **9** |
| `JobRoleID` vides | **0** |
| Lignes `Dim_JobRole` | **9** |
| `JobRoleID` distincts dans la dimension | **9** |

Les résultats correspondent aux valeurs attendues.

---

# 10. Contrôles de cohérence métier

Au-delà des contrôles techniques, plusieurs vérifications de cohérence métier ont été effectuées.

Les variables liées à l'expérience, au rôle, au manager et à la promotion ont notamment été contrôlées.

| Contrôle | Nombre d'incohérences |
|---|---:|
| Experience | **0** |
| Role | **0** |
| Manager | **0** |
| Promotion | **0** |

Aucune incohérence n'a été détectée lors de ces contrôles.

---

# 11. Préparation de Fact_Employee

Après les différentes étapes de préparation et de contrôle, la table analytique principale :

```text
Fact_Employee
```

a été construite.

### Contrôle final

```text
Nombre de lignes = 1 470
```

Le nombre de lignes est resté identique à celui du dataset initial.

Cela permet de confirmer que les opérations de préparation et de fusion n'ont entraîné aucune perte d'employé.

---

# 12. Flux général de préparation

Le processus de préparation peut être résumé ainsi :

```text
Dataset initial
      │
      ▼
Contrôle de la structure
      │
      ▼
Vérification des types
      │
      ▼
Contrôle de la qualité
      │
      ▼
Analyse des valeurs distinctes
      │
      ▼
Identification des variables constantes
      │
      ▼
Contrôle de EmployeeNumber
      │
      ▼
Création de DepartmentID
      │
      ▼
Fusion avec Dim_Department
      │
      ▼
Création de JobRoleID
      │
      ▼
Fusion avec Dim_JobRole
      │
      ▼
Contrôles de cohérence
      │
      ▼
Fact_Employee
```

---

# 13. Résultats finaux

| Contrôle | Résultat |
|---|---:|
| Lignes initiales | **1 470** |
| Lignes `Fact_Employee` | **1 470** |
| EmployeeNumber distincts | **1 470** |
| DepartmentID distincts | **3** |
| DepartmentID nulls | **0** |
| JobRoleID distincts | **9** |
| JobRoleID vides | **0** |
| Lignes `Dim_JobRole` | **9** |
| Qualité des colonnes | **100 %** |
| Incohérences métier détectées | **0** |

---

# 14. Conclusion

La préparation avec Power Query a permis de transformer le dataset initial en une base structurée et contrôlée, prête à être utilisée dans le modèle analytique Power BI.

Les contrôles réalisés ont permis de vérifier :

- l'intégrité des données ;
- l'unicité des employés ;
- la cohérence des clés ;
- l'intégrité des fusions ;
- l'absence de valeurs manquantes sur les clés créées ;
- la cohérence de certaines relations métier.

La table `Fact_Employee` constitue ensuite la base des analyses et des mesures DAX utilisées dans le dashboard.

---

## Technologies

- **Power BI**
- **Power Query**
- **Power Query M**
- **Data Cleaning**
- **Data Transformation**
- **Data Quality**
- **Data Modeling**