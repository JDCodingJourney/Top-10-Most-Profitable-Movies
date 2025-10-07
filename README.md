# Top-10-Most-Profitable-Movies
This project analyzes the Top 500 highest-grossing Hollywood movies of all time. The dataset includes movie titles, release years, budgets, domestic and international grosses, and worldwide revenues.

The main goal was to:
Explore and clean the dataset using SQL (MySQL Workbench).
Calculate a key metric: Return on Investment (ROI).
Identify trends and insights (e.g., most profitable movies).
Prepare the data for visualization in Tableau.

Dataset

Columns of interest:

title → Movie name.

year → Release year.

budget_millions → Production budget in millions (cleaned).

worldwide_gross → Worldwide box office revenue (cleaned).

domestic_gross, international_gross → Split revenue.

source, budget source → Dropped (incomplete and irrelevant).

Initial dataset: 1118 rows
After cleaning duplicates: ~558 unique movies.

 ## View all records ##
 ```SELECT * 
FROM project.top500;

SELECT count(*)
FROM top500;```

## Confirm the initial dataset size. We found 1118 records. ## 

```SELECT COUNT(NULLIF(TRIM(source), ''))
FROM top500;```

## Check duplicated titles ##
```SELECT COUNT(title) 
FROM top500 
GROUP BY title 
HAVING COUNT(title) > 1;```

## Count unique movies (by title and year) ##

```SELECT Count(*)
FROM (SELECT DISTINCT title, year
	  FROM top500) AS sub
      ;```
      
## Column Cleaning ##

-- Verify columns with problematic names
      
```SELECT `worldwide gross (m)` AS worldwide_gross, `budget_millions`
FROM top500;```

-- Rename columns for analysis

```ALTER TABLE top500 
CHANGE `worldwide gross (m)` worldwide_gross DECIMAL(15,2);

ALTER TABLE top500 
CHANGE `budget_millions` budget_millions DECIMAL(15,2);```

## Count non-null values in source-related columns ##

```SELECT 
    COUNT(source) AS source_not_null,
    COUNT(`budget source`) AS budget_source_not_null
FROM top500;
;```


## ROI calculated for all movies ##

```SELECT
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
ORDER BY ROI DESC;```


## Top 10 Most Profitable Movies ##

```SELECT
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
LIMIT 10;```

## Resure ROI view with a temporaty table ##

```CREATE VIEW roi_view AS
SELECT 
    title,
    year,
    worldwide_gross_income,
    production_budget,
    (worldwide_gross_income - production_budget) / production_budget * 100 AS roi
FROM top500
WHERE worldwide_gross_income IS NOT NULL 
  AND production_budget IS NOT NULL;```




