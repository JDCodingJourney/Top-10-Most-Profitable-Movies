# 🎬 Top 500 Hollywood Movies — SQL & Tableau Analysis

> A complete SQL project exploring profitability in the film industry using ROI analysis and data visualization.

---

## 📘 Project Overview
This project analyzes the **Top 500 highest-grossing Hollywood movies of all time** using SQL for data exploration and Tableau for visualization.  
The goal is to understand which films achieved the highest **Return on Investment (ROI)** and what patterns emerge across time.

---

## ⚙️ Tools Used
- **MySQL Workbench** → Data cleaning, transformations, and analysis.  
- **Tableau Public** → Data visualization and storytelling.  
- **GitHub** → Project version control and portfolio presentation.  

---

## 📂 Dataset Overview
The dataset includes **1118 records** of Hollywood movies with the following key columns:

| Column | Description |
|:--|:--|
| `title` | Movie title |
| `year` | Release year |
| `worldwide gross (m)` | Global box office revenue (in millions) |
| `budget (millions)` | Production budget (in millions) |
| `domestic gross (m)` | U.S. box office |
| `international gross (m)` | Overseas box office |
| `source`, `budget source` | Information links (mostly empty, dropped later) |

---

## 🧹 Data Exploration and Cleaning

### 1️⃣ Checking Total Number of Records
The first step is to verify the total number of records in the dataset.

```sql
SELECT COUNT(*) 
FROM top500;
```

##<img width="1875" height="355" alt="image" src="https://github.com/user-attachments/assets/e0c2c8d5-b0fb-4d2b-b926-010055a14c17" />##

Result: 1118 records found in the raw dataset.

2️⃣ Identifying Duplicate Movies

Each movie title may appear more than once (e.g., re-releases or extended editions).
We check for duplicates based on title and year.

```
SELECT COUNT(*)
FROM (
    SELECT DISTINCT title, year
    FROM top500
) AS sub;
```

<img width="102" height="352" alt="image" src="https://github.com/user-attachments/assets/484d8ca8-84c8-45bd-94ed-c177c4072490" />

Result: 558 unique movies after removing duplicates.

3️⃣ Renaming Columns

Some column names contained spaces and parentheses, making them difficult to query.
We renamed them for clarity and consistency.

```
ALTER TABLE top500 
CHANGE `worldwide gross (m)` worldwide_gross DECIMAL(15,2);

ALTER TABLE top500 
CHANGE `budget (millions)` budget_millions DECIMAL(15,2);
```

<img width="534" height="47" alt="image" src="https://github.com/user-attachments/assets/8462e4a1-a134-4279-b685-283f1d17a569" />

Columns cleaned: worldwide_gross, budget_millions

4️⃣ Cleaning Unnecessary Columns

The source and budget source columns contained mostly null or empty values and were removed from analysis.

```
SELECT 
    COUNT(source) AS source_not_null,
    COUNT(`budget source`) AS budget_source_not_null
FROM top500;
```

<img width="253" height="43" alt="image" src="https://github.com/user-attachments/assets/106fd3ab-76f5-4e5f-9e71-fe8bbc7c0c79" />

Only ~85% of rows contained useful entries → columns dropped.

ROI Calculation

We calculate Return on Investment (ROI) as:

ROI = (worldwide_gross / budget_millions) × 100

```
SELECT
    title,
    year,
    worldwide_gross,
    budget_millions,
    ROUND((worldwide_gross / budget_millions) * 100, 2) AS ROI
FROM (
    SELECT DISTINCT
        title,
        year,
        worldwide_gross,
        budget_millions
    FROM top500
) AS unique_movies
ORDER BY ROI DESC;

```

<img width="455" height="355" alt="image" src="https://github.com/user-attachments/assets/fd69d82b-c691-415c-9a99-e05fcde467ad" />


Result: ROI calculated for all unique movies, rounded to two decimals.

Top 10 Most Profitable Movies

We rank the top 10 movies with the highest ROI.

```
SELECT
    title,
    year,
    worldwide_gross,
    budget_millions,
    ROUND((worldwide_gross / budget_millions) * 100, 2) AS ROI
FROM (
    SELECT DISTINCT
        title,
        year,
        worldwide_gross,
        budget_millions
    FROM top500
) AS unique_movies
ORDER BY ROI DESC
LIMIT 10;
```

<img width="459" height="192" alt="image" src="https://github.com/user-attachments/assets/31a0b4e2-5e3c-4271-843f-ce478047e728" />

The highest ROI often comes from low-budget films that became global hits.

Creating a Reusable ROI View

To avoid recalculating the ROI formula in multiple queries, we create a SQL view named roi_view.
This view stores the calculated ROI for each movie using the cleaned dataset.
It helps streamline future analyses — for example, grouping ROI by decade or visualizing results in Tableau

```
CREATE VIEW roi_view AS
SELECT 
    title,
    year,
    worldwide_gross_income,
    production_budget,
    (worldwide_gross_income - production_budget) / production_budget * 100 AS roi
FROM top500
WHERE worldwide_gross_income IS NOT NULL 
  AND production_budget IS NOT NULL;
```


**Insights

The dataset initially contained duplicates and unstructured columns that required cleaning.

ROI revealed a very different ranking compared to total revenue — some small productions far outperformed blockbusters.

High ROI correlates more strongly with budget control than with raw box office success.

Creating a reusable SQL view (roi_view) helps simplify future queries and Tableau integrations.
**
