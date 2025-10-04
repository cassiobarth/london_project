# Transport for London (TfL) Journey Analysis with SQL and Snowflake

## Project Overview

This project performs an exploratory data analysis (EDA) on a dataset of public transport journey volumes from Transport for London (TfL), the local government body responsible for London's transport system. London, a city of over 8.5 million residents, relies heavily on its extensive public transport network, which includes the Underground, buses, trams, and river services.

The analysis is conducted entirely through SQL queries on a **Snowflake** database, aiming to uncover key insights into ridership patterns, the popularity of different transport methods, and historical usage trends.

## Tech Stack

* **Database:** Snowflake
* **Language:** SQL

## Data Source

The data is a modified version of a dataset made public by the Mayor of London's office. It has been loaded into a Snowflake database named `TFL` with a single table, `JOURNEYS`.

### Table Schema: `TFL.JOURNEYS`

| Column            | Definition                                   | Data type |
| ----------------- | -------------------------------------------- | --------- |
| `MONTH`           | Month in number format, e.g., `1` equals January | `INTEGER`   |
| `YEAR`            | Year                                         | `INTEGER`   |
| `DAYS`            | Number of days in the given month            | `INTEGER`   |
| `REPORT_DATE`     | Date that the data was reported              | `DATE`      |
| `JOURNEY_TYPE`    | Method of transport used                     | `VARCHAR`   |
| `JOURNEYS_MILLIONS` | Millions of journeys, measured in decimals     | `FLOAT`     |

## Analysis & SQL Queries

The analysis answers three key questions about the journey data.

### 1. What is the total journey volume for each transport type?

This query aggregates the total number of journeys (in millions) for every `JOURNEY_TYPE` and orders them to reveal the most and least used forms of public transport in the dataset.

```sql
SELECT
    JOURNEY_TYPE,
    SUM(JOURNEYS_MILLIONS) AS TOTAL_JOURNEYS_MILLIONS
FROM TFL.JOURNEYS
GROUP BY JOURNEY_TYPE
ORDER BY TOTAL_JOURNEYS_MILLIONS DESC;
```

### 2. What were the top 5 busiest months for the Emirates Airline?

This query filters the data to focus solely on the 'Emirates Airline' cable car, identifying the five months with the highest volume of passengers. The results are rounded for clarity.

```sql
SELECT
    MONTH,
    YEAR,
    ROUND(JOURNEYS_MILLIONS, 2) AS ROUNDED_JOURNEYS_MILLIONS
FROM
    TFL.JOURNEYS
WHERE
    JOURNEY_TYPE = 'Emirates Airline'
    AND JOURNEYS_MILLIONS IS NOT NULL
ORDER BY ROUNDED_JOURNEYS_MILLIONS DESC
LIMIT 5;
```

### 3. Which 5 years had the lowest Underground & DLR usage?

This query investigates historical lows for the city's primary rail systems. It groups data by year, sums the journeys for services including 'Underground', and retrieves the five years with the smallest totals, which could correspond to significant city-wide events or disruptions.

```sql
SELECT
    YEAR,
    JOURNEY_TYPE,
    SUM(JOURNEYS_MILLIONS) AS TOTAL_JOURNEYS_MILLIONS
FROM
    TFL.JOURNEYS
WHERE
    JOURNEY_TYPE LIKE '%Underground%'
GROUP BY
    YEAR, JOURNEY_TYPE
ORDER BY TOTAL_JOURNEYS_MILLIONS ASC
LIMIT 5;
```

## How to Reproduce

To reproduce this analysis, you would need access to a Snowflake environment with the `TFL.JOURNEYS` table loaded. The SQL queries listed above can then be executed in a Snowflake worksheet or via any connected SQL client.
