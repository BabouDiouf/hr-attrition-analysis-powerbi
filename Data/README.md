# Data — HR Attrition Analysis

Ce dossier est dédié aux données utilisées dans le projet **HR Attrition Analysis** réalisé avec Power BI.

---

## Dataset

Le projet utilise le dataset :

**IBM HR Analytics Employee Attrition & Performance**

Ce dataset contient des informations relatives aux employés d'une organisation et permet d'étudier les facteurs associés à leur départ.

---

## Contenu des données

Le dataset contient initialement :

- **1 470 employés**
- **35 variables**

Les variables couvrent plusieurs catégories d'informations :

### Informations démographiques

- Age
- Gender
- MaritalStatus
- Education
- EducationField

### Informations professionnelles

- Department
- JobRole
- JobLevel
- BusinessTravel
- OverTime
- JobInvolvement

### Rémunération

- DailyRate
- HourlyRate
- MonthlyIncome
- MonthlyRate
- PercentSalaryHike
- StockOptionLevel

### Satisfaction et performance

- EnvironmentSatisfaction
- JobSatisfaction
- RelationshipSatisfaction
- WorkLifeBalance
- PerformanceRating

### Expérience et ancienneté

- TotalWorkingYears
- YearsAtCompany
- YearsInCurrentRole
- YearsSinceLastPromotion
- YearsWithCurrManager
- NumCompaniesWorked

### Variable cible

- **Attrition** — indique si l'employé a quitté l'entreprise (`Yes`) ou est resté (`No`).

---

## Contrôles réalisés

Avant la modélisation, plusieurs contrôles de qualité ont été effectués.

### Identifiant employé

`EmployeeNumber` contient :

**1 470 valeurs distinctes pour 1 470 employés.**

L'identifiant est donc unique dans le dataset.

### Variables constantes

Les variables suivantes ne présentent qu'une seule valeur distincte :

- `EmployeeCount`
- `StandardHours`
- `Over18`

Elles présentent donc une faible valeur analytique dans le contexte de cette étude.

### Qualité générale

Les contrôles réalisés dans Power Query n'ont révélé aucune valeur invalide dans les colonnes analysées.

---

## Préparation des données

Les données ont été préparées dans **Power Query** avant leur utilisation dans le modèle Power BI.

Les principales opérations comprennent :

- vérification des types de données ;
- contrôle des valeurs distinctes ;
- contrôle des valeurs manquantes ;
- identification des variables constantes ;
- vérification des identifiants ;
- création de `DepartmentID` ;
- création de `JobRoleID` ;
- contrôles de cohérence métier.

La table analytique finale `Fact_Employee` contient :

**1 470 lignes.**

---

## Données et confidentialité

Le dataset utilisé dans ce projet est un jeu de données d'analyse destiné à l'apprentissage et à la démonstration.

Aucune donnée personnelle réelle provenant de l'entreprise de l'auteur n'est utilisée dans ce projet.

Le fichier de données brut n'est pas nécessairement inclus directement dans ce repository afin de conserver un dépôt léger et de respecter les conditions de redistribution applicables à la source du dataset.

---

## Documentation associée

Pour comprendre la transformation et l'utilisation des données :

- [Préparation des données avec Power Query](../Documentation/Power_Query.md)
- [Modèle de données](../Documentation/Data_Model.md)
- [Mesures DAX](../Documentation/DAX_Measures.md)

---

## Utilisation

Pour reproduire l'analyse :

1. Obtenir le dataset source.
2. Ouvrir le fichier Power BI situé dans le dossier `PowerBI/`.
3. Vérifier la source de données dans Power Query.
4. Actualiser les données si nécessaire.
5. Explorer les trois pages du dashboard.

---

## Résumé

| Élément | Valeur |
|---|---:|
| Nombre d'employés | **1 470** |
| Variables initiales | **35** |
| Employés ayant quitté l'entreprise | **237** |
| Employés restants | **1 233** |
| Taux d'attrition | **16,12 %** |

---

**Projet : HR Attrition Analysis — Power BI**

**Technologies :** Power BI · Power Query · DAX · Git · GitHub