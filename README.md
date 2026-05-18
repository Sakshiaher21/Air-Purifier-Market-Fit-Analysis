# 🌍 Air Purifier Market Fit Research Using AQI Analytics

## Strategic AQI Analytics for Product Development & Market Expansion

This repository contains SQL scripts and Power BI analysis developed to support market research for **AirPure Innovations**, a startup evaluating opportunities in the Indian air purifier market.

The project analyzes AQI patterns, pollution severity, dominant pollutants, EV adoption trends, and market demand indicators to identify strategic regions for air purifier product development and expansion.

---

# Introduction

Air pollution has become one of India's growing environmental and public health concerns. Multiple cities frequently rank among the world's most polluted regions, creating strong demand for air quality solutions.

Before investing heavily in R&D and product development, AirPure Innovations wanted data-driven answers to critical business questions:

* Which pollutants should future air purifiers target?
* Which cities show strongest market demand?
* Which regions should be prioritized?
* How can product strategy align with localized pollution patterns?

This project uses SQL and Power BI to convert AQI datasets into business insights and recommendations.

---

# Data Sources

Analysis was performed using datasets collected from the Dataful platform and supporting datasets.

Primary datasets include:

* AQI Dataset (2024–2025)
* EV Adoption Dataset
* Population Dataset
* Pollution Indicators
* Geographic Data

These datasets contain information related to:

* AQI values
* Cities and states
* Dominant pollutants
* Vehicle and EV trends
* Population statistics

---

# Project Overview

Analyzed AQI and related datasets using SQL and Power BI.

Performed ad-hoc analysis to answer business questions and identify market opportunities.

Insights are intended to support product development strategy and regional expansion.

---

# ADHOC ANALYSIS

## 1. Top AQI Regions

**Objective:** Identify regions with highest AQI severity.

```sql
SELECT distinct area,avg(aqi_value) as max_aqi
FROM "air_quality"
group by area
order by max_aqi desc
limit 5;
```

Insight:
Helped identify high-priority regions for purifier expansion.

---

## 2. Pollutant Analysis Across States

**Objective:** Identify dominant pollutants across Indian states.

```sql
WITH COUNTS AS(
select state,
prominent_pollutants, 
count(prominent_pollutants) as count 
from "air_quality"
group by state,prominent_pollutants),
T2 AS(
SELECT *,
ROW_NUMBER() OVER (PARTITION BY STATE ORDER BY COUNT DESC) AS TOP_RN,
ROW_NUMBER() OVER (PARTITION BY STATE ORDER BY COUNT) AS BOTTOM_RANK 
FROM COUNTS)
SELECT * FROM T2
WHERE TOP_RN<=2 
order by state asc,top_rn desc;
```

Insight:
PM10 and PM2.5 emerged as major pollutants.

---

## 3. Weekend vs Weekday AQI Analysis

**Objective:** Determine whether AQI improves during weekends in Metro cities.

```sql
SELECT 
    area,
    CASE 
        WHEN date_part('dow', date) IN (0,6) THEN 'Weekend'
        ELSE 'Weekday'
    END AS day_type,
    AVG(aqi_value) AS avg_aqi
FROM "air_quality"
WHERE area IN ('Mumbai', 'Chennai', 'Kolkata', 'Bengaluru', 'Hyderabad', 'Ahmedabad', 'Pune','Delhi')
AND date >= CURRENT_DATE - INTERVAL '1 year'
GROUP BY area, day_type
ORDER BY area;

```

Insight:
Helped identify traffic-related pollution behavior.

---

## 4. Seasonal AQI Trend Analysis

**Objective:** Identify months showing severe pollution.
(Considerig top 10 states with high distinct areas)

```sql
with t1 as(select state ,count(distinct area) as area
from "air_quality"
group by state 
order by area desc
limit 10),
t2 as(select state,date_part('month',date) as month, avg(aqi_value) as aqi
from air_quality
where state in(select state from t1)
group by state,month
order by aqi desc),
t3 as (select *,
        row_number() over (partition by state order by aqi desc ) as rank_no
		from t2)
select  * from t3
		where rank_no<= 2;
```

Insight:
Seasonal spikes indicate possible purifier demand surges.

---

## 5. EV Adoption Analysis

**Objective:** Evaluate AQI differences based on EV adoption trends.

```sql
SELECT 
        state,
        SUM(value) AS total_ev
    FROM "Vehicle_data"
    WHERE fuel IN (
        'ELECTRIC(BOV)')
    GROUP BY state;
SELECT 
    state,
    SUM(
        CASE 
            WHEN fuel = 'ELECTRIC(BOV)' 
            THEN value 
            ELSE 0 
        END
    ) AS total_ev,
    SUM(value) AS total_vehicles,
    ROUND(
        ( SUM(
             CASE 
                    WHEN fuel = 'ELECTRIC(BOV)' 
                    THEN value 
                    ELSE 0 
                END
            ) * 100.0
        ) / SUM(value),
        2
    ) AS ev_adoption_percentage
FROM "Vehicle_data"
GROUP BY state
ORDER BY ev_adoption_percentage DESC;e
```

Insight:
Explored relationship between sustainability trends and AQI.

---

# Dashboard Highlights

The Power BI dashboard provides:

* National Average AQI KPI
* Dominant Pollutant Identification
* Highest Risk City Detection
* EV Adoption Metrics
* AQI Trend Analysis
* Population vs AQI Relationship
* Priority Market Region Analysis
* Seasonal Pollution Trends

---

# Results & Key Insights

### Dominant Pollutants

PM10 and PM2.5 emerged as dominant pollutants across major regions.

### Seasonal Trend

AQI levels increase significantly during winter periods.

### Market Opportunities

Northern regions demonstrated stronger pollution severity and demand potential.

### Population vs AQI

Population alone does not determine AQI severity; industrial activity and traffic density also contribute significantly.

---

# Visualizations

Explore interactive dashboard:

[View Live Power BI Dashboard](https://app.powerbi.com/view?r=eyJrIjoiNzViNDVlNGUtMTQ1My00OTlkLTgxMjItZjg0YWU2NDI2YTc3IiwidCI6IjM3ZDZjMmRjLTQ4MTMtNDhlMy1hODQyLTI4Y2FiODE3MWM5OCIsImMiOjEwfQ%3D%3D&pageName=09f8b9563c5833bb7efa)

---

# Project Structure

```
AQI-AirPurifier-Market-Research
│
├── ADHOC ANALYSIS
│   ├── AQI_Severity.sql
│   ├── Pollutant_Analysis.sql
│   ├── AQI_Trends.sql
│   ├── EV_Analysis.sql
│
├── Dashboard
│   └── dashboard.png
│
├── PPT
│   └── Project_Presentation.pptx
│
└── README.md
```

---

# Future Scope

Potential future enhancements:

* Integrate Google Trends data
* Add health datasets
* Include live AQI APIs
* Add predictive models
* Create city-level demand simulations

---

# Conclusion

This project provides strategic insights into pollution patterns and market opportunities using AQI analytics. By combining SQL and Power BI, AirPure Innovations can make informed product development and market expansion decisions.

---

👩‍💻 Created By: Sakshi Aher

Aspiring Data Analyst | SQL | Power BI | Python

