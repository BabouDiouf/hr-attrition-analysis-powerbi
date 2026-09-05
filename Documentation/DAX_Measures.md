# DAX Measures — HR Attrition Analysis

## Présentation

Les mesures **DAX (Data Analysis Expressions)** ont été développées dans Power BI afin de calculer les principaux indicateurs RH et d'analyser les facteurs associés à l'attrition des employés.

Les mesures ont été organisées autour de plusieurs axes :

- effectif ;
- attrition ;
- rémunération ;
- département ;
- OverTime ;
- fonction ;
- satisfaction ;
- ancienneté ;
- analyses croisées.

L'objectif est de privilégier des **mesures dynamiques**, capables de s'adapter automatiquement aux filtres et aux interactions du dashboard.

---

# 1. Mesures d'effectif

## Nombre Employés

```DAX
Nombre Employés =
DISTINCTCOUNT(
    Fact_Employee[EmployeeNumber]
)
```

### Objectif

Calculer le nombre unique d'employés présents dans le contexte de filtre actuel.

### Résultat global

**1 470 employés**

### Pourquoi `DISTINCTCOUNT` ?

L'utilisation de `DISTINCTCOUNT` permet d'éviter de compter plusieurs fois un même employé si plusieurs lignes lui étaient associées.

Dans ce projet, `EmployeeNumber` est unique, ce qui garantit un comptage fiable.

---

# 2. Mesures d'attrition

## Employés Ayant Quitté

```DAX
Employés Ayant Quitté =
CALCULATE(
    [Nombre Employés],
    Fact_Employee[Attrition] = "Yes"
)
```

### Objectif

Calculer le nombre d'employés ayant quitté l'entreprise.

### Résultat global

**237 employés**

### Logique

`CALCULATE` modifie le contexte de filtre afin de ne conserver que les employés dont :

```text
Attrition = "Yes"
```

---

## Employés Restants

```DAX
Employés Restants =
CALCULATE(
    [Nombre Employés],
    Fact_Employee[Attrition] = "No"
)
```

### Résultat global

**1 233 employés**

Cette mesure permet notamment de comparer les caractéristiques des employés restants à celles des employés ayant quitté l'entreprise.

---

## Taux Attrition

```DAX
Taux Attrition =
DIVIDE(
    [Employés Ayant Quitté],
    [Nombre Employés],
    0
)
```

### Objectif

Calculer le pourcentage d'employés ayant quitté l'entreprise.

### Résultat global

**16,12 %**

### Pourquoi `DIVIDE` ?

`DIVIDE` est préférable à une division classique car elle permet de gérer proprement les situations où le dénominateur serait nul.

---

# 3. Mesures de rémunération

## Revenu Mensuel Total

```DAX
Revenu Mensuel Total =
SUM(
    Fact_Employee[MonthlyIncome]
)
```

### Résultat

Environ :

**10 M**

Cette mesure représente la somme des revenus mensuels des employés présents dans le contexte de filtre.

---

## Revenu Mensuel Moyen

```DAX
Revenu Mensuel Moyen =
AVERAGE(
    Fact_Employee[MonthlyIncome]
)
```

### Résultat global

**6,50 K**

Cette mesure permet d'étudier le niveau moyen de rémunération selon différents groupes d'employés.

---

## Revenu Mensuel Médian

```DAX
Revenu Mensuel Médian =
MEDIAN(
    Fact_Employee[MonthlyIncome]
)
```

### Résultat global

**4,92 K**

### Pourquoi utiliser la médiane ?

La médiane permet de compléter l'analyse de la moyenne lorsqu'une distribution peut être influencée par des valeurs élevées.

La comparaison :

```text
Moyenne ≠ Médiane
```

permet donc de mieux comprendre la distribution des revenus.

---

# 4. Comparaison de la rémunération selon l'attrition

## Revenu Moyen — Employés Ayant Quitté

```DAX
Revenu Moyen - Employés Ayant Quitté =
CALCULATE(
    [Revenu Mensuel Moyen],
    Fact_Employee[Attrition] = "Yes"
)
```

### Résultat

**4,79 K**

---

## Revenu Moyen — Employés Restants

```DAX
Revenu Moyen - Employés Restants =
CALCULATE(
    [Revenu Mensuel Moyen],
    Fact_Employee[Attrition] = "No"
)
```

### Résultat

**6,83 K**

---

## Écart Revenu Attrition

```DAX
Écart Revenu Attrition =
[Revenu Moyen - Employés Restants]
    - [Revenu Moyen - Employés Ayant Quitté]
```

### Résultat

**2,05 K**

### Interprétation

Dans les données analysées, le revenu mensuel moyen des employés restants est supérieur à celui des employés ayant quitté l'entreprise.

Cet indicateur permet de mettre en évidence cet écart de rémunération.

---

# 5. Analyse de l'attrition par département

La mesure principale utilisée est :

```DAX
Taux Attrition =
DIVIDE(
    [Employés Ayant Quitté],
    [Nombre Employés],
    0
)
```

Grâce au contexte de filtre Power BI, la même mesure peut être utilisée pour chaque département.

### Résultats

| Department | Taux d'attrition |
|---|---:|
| Sales | **20,63 %** |
| Research & Development | **13,84 %** |
| Human Resources | **19,05 %** |

### Principe

Lorsque `Department` est placé dans un visuel, Power BI applique automatiquement le contexte correspondant à chaque département.

Il n'est donc pas nécessaire de créer une mesure différente pour chaque département.

---

# 6. Analyse de l'OverTime

La même logique de contexte permet d'analyser l'attrition selon :

```text
OverTime = Yes
OverTime = No
```

### Résultats

| OverTime | Taux d'attrition |
|---|---:|
| Yes | **30,53 %** |
| No | **10,44 %** |

### Écart

```text
30,53 % - 10,44 %
= 20,09 points
```

Cet écart constitue l'un des principaux insights du projet.

---

# 7. Analyse croisée Department × OverTime

La mesure :

```DAX
Taux Attrition
```

est utilisée dans une analyse croisée entre :

- `Department`
- `OverTime`

### Résultats

| Department | OverTime | Taux d'attrition |
|---|---|---:|
| Human Resources | No | **15,22 %** |
| Human Resources | Yes | **29,41 %** |
| Research & Development | No | **8,55 %** |
| Research & Development | Yes | **27,31 %** |
| Sales | No | **13,84 %** |
| Sales | Yes | **37,50 %** |

### Insight

L'attrition est supérieure chez les employés soumis à l'OverTime dans les trois départements.

La différence est particulièrement importante dans le département **Sales**.

---

# 8. Analyse par fonction

La mesure :

```DAX
Taux Attrition
```

est également utilisée avec `JobRole`.

### Principaux résultats

| JobRole | Taux d'attrition |
|---|---:|
| Sales Representative | **39,76 %** |
| Laboratory Technician | **23,94 %** |
| Human Resources | **23,08 %** |
| Sales Executive | **17,48 %** |
| Research Scientist | **16,10 %** |
| Manufacturing Director | **6,90 %** |
| Healthcare Representative | **6,87 %** |
| Manager | **4,90 %** |
| Research Director | **2,50 %** |

### Insight

Les niveaux d'attrition varient fortement selon la fonction.

Les **Sales Representatives** présentent notamment un taux d'attrition particulièrement élevé.

---

# 9. Analyse par niveau hiérarchique

La mesure `Taux Attrition` permet également d'analyser `JobLevel`.

| JobLevel | Taux d'attrition |
|---:|---:|
| 1 | **26,34 %** |
| 2 | **9,74 %** |
| 3 | **14,68 %** |
| 4 | **4,72 %** |
| 5 | **7,25 %** |

Cette analyse permet d'identifier les niveaux hiérarchiques présentant les taux d'attrition les plus élevés.

---

# 10. Analyse de la satisfaction

## Job Satisfaction

Le taux d'attrition est analysé selon `JobSatisfaction`.

| JobSatisfaction | Taux d'attrition |
|---:|---:|
| 1 | **22,84 %** |
| 2 | **16,43 %** |
| 3 | **16,52 %** |
| 4 | **11,33 %** |

### Insight

Le niveau de satisfaction le plus faible présente un taux d'attrition supérieur au niveau de satisfaction le plus élevé.

---

## Environment Satisfaction

| EnvironmentSatisfaction | Taux d'attrition |
|---:|---:|
| 1 | **25,35 %** |
| 2 | **14,98 %** |
| 3 | **13,69 %** |
| 4 | **13,45 %** |

Cette analyse permet d'étudier l'association entre la satisfaction vis-à-vis de l'environnement de travail et l'attrition.

---

# 11. Analyse de l'ancienneté

Un groupe d'ancienneté a été créé afin de faciliter l'analyse.

| Groupe | Taux d'attrition |
|---|---:|
| Début | **29,82 %** |
| Courte | **13,82 %** |
| Moyenne | **12,28 %** |
| Très longue | **12,12 %** |
| Longue | **6,67 %** |

### Insight

Le groupe **Début** présente le taux d'attrition le plus élevé.

---

# 12. Analyse Ancienneté × OverTime

L'analyse croisée permet d'aller plus loin.

| Groupe Ancienneté | OverTime | Taux d'attrition |
|---|---|---:|
| Courte | No | **7,82 %** |
| Courte | Yes | **28,35 %** |
| Début | No | **20,59 %** |
| Début | Yes | **50,96 %** |
| Longue | No | **5,84 %** |
| Longue | Yes | **9,30 %** |
| Moyenne | No | **7,32 %** |
| Moyenne | Yes | **25,83 %** |
| Très longue | No | **11,36 %** |
| Très longue | Yes | **13,64 %** |

### Insight principal

La combinaison :

**Début + OverTime**

présente un taux d'attrition de :

> **50,96 %**

Cette segmentation permet d'aller au-delà d'une simple analyse individuelle des variables.

---

# 13. Analyse JobRole × OverTime

L'analyse croisée entre `JobRole` et `OverTime` permet d'identifier les populations particulièrement exposées.

### Exemple

**Sales Representative + OverTime**

> **66,67 %**

**Laboratory Technician + OverTime**

> **50,00 %**

**Research Scientist + OverTime**

> **34,02 %**

**Sales Executive + OverTime**

> **32,98 %**

Ces résultats permettent d'identifier des segments nécessitant une analyse RH plus approfondie.

---

# 14. Analyse WorkLifeBalance × OverTime

L'analyse croisée permet également d'étudier la relation entre équilibre vie professionnelle/personnelle et OverTime.

| WorkLifeBalance | OverTime | Taux d'attrition |
|---:|---|---:|
| 1 | No | **25,86 %** |
| 1 | Yes | **45,45 %** |
| 2 | No | **10,83 %** |
| 2 | Yes | **30,77 %** |
| 3 | No | **8,45 %** |
| 3 | Yes | **28,74 %** |
| 4 | No | **12,82 %** |
| 4 | Yes | **33,33 %** |

Cette analyse permet d'étudier simultanément plusieurs dimensions RH.

---

# 15. Principe DAX utilisé dans le projet

Une grande partie du dashboard repose sur une mesure centrale :

```DAX
Taux Attrition =
DIVIDE(
    [Employés Ayant Quitté],
    [Nombre Employés],
    0
)
```

Cette mesure est ensuite réutilisée avec différents contextes de filtre :

```text
Department
      ↓
Taux Attrition
```

```text
JobRole
      ↓
Taux Attrition
```

```text
OverTime
      ↓
Taux Attrition
```

```text
JobRole + OverTime
      ↓
Taux Attrition
```

```text
Ancienneté + OverTime
      ↓
Taux Attrition
```

Cette approche évite de créer une multitude de mesures spécifiques et permet de conserver un modèle DAX plus simple et plus maintenable.

---

# 16. Bonnes pratiques DAX appliquées

### `DISTINCTCOUNT`

Utilisé pour compter les employés de manière fiable.

### `CALCULATE`

Utilisé pour modifier le contexte de filtre et analyser des populations spécifiques.

### `DIVIDE`

Utilisé pour effectuer les divisions de manière sécurisée.

### Mesures réutilisables

Les mesures principales sont réutilisées dans différents visuels et contextes.

Cette approche facilite :

- la maintenance ;
- la lisibilité ;
- la cohérence des KPI ;
- l'interactivité du dashboard.

---

# 17. Interprétation des résultats

Les mesures DAX permettent de mettre en évidence des **associations statistiques descriptives** dans les données.

Elles ne permettent pas, à elles seules, d'établir une causalité.

Par exemple :

> Les employés soumis à l'OverTime présentent un taux d'attrition plus élevé.

Cela ne signifie pas nécessairement :

> L'OverTime est la cause directe de leur départ.

Une analyse causale ou prédictive nécessiterait des données supplémentaires et des méthodes statistiques adaptées.

---

# 18. Synthèse des principaux KPI

| KPI | Résultat |
|---|---:|
| Nombre d'employés | **1 470** |
| Employés ayant quitté | **237** |
| Employés restants | **1 233** |
| Taux d'attrition | **16,12 %** |
| Revenu mensuel total | **≈ 10 M** |
| Revenu mensuel moyen | **6,50 K** |
| Revenu mensuel médian | **4,92 K** |
| Revenu moyen — employés partis | **4,79 K** |
| Revenu moyen — employés restants | **6,83 K** |
| Écart revenu attrition | **2,05 K** |

---

# Conclusion

Les mesures DAX développées dans ce projet constituent la couche analytique du modèle Power BI.

Elles permettent de passer de données individuelles sur les employés à des indicateurs permettant d'analyser :

- l'effectif ;
- l'attrition ;
- la rémunération ;
- les départements ;
- les fonctions ;
- l'ancienneté ;
- l'OverTime ;
- la satisfaction ;
- les interactions entre plusieurs facteurs.

L'utilisation du contexte de filtre Power BI permet notamment de construire des analyses multidimensionnelles à partir d'un nombre limité de mesures réutilisables.

**Technologies :** DAX · Power BI · Data Modeling · Business Intelligence