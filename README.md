# PwC Call Centre Performance & Customer Retention Analysis

[![Power BI](https://img.shields.io/badge/Power_BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)](https://powerbi.microsoft.com/)
[![DAX](https://img.shields.io/badge/DAX-Data_Analysis_Expressions-yellowgreen?style=for-the-badge)](https://learn.microsoft.com/en-us/dax/)
[![Data Analytics](https://img.shields.io/badge/Analytics-PwC_Job_Simulation-blue?style=for-the-badge)](https://www.theforage.com/)

An end-to-end Business Intelligence solution developed as part of the **PwC Switzerland Power BI Job Simulation on Forage**. This project converts raw, unstructured customer service call records into an interactive executive dashboard designed for Call Centre Managers to evaluate operational efficiency, agent performance, and overall customer satisfaction (CSAT).

---

## Dashboard Preview

![Call Centre Dashboard](PWC%20Report%20Dashboard%20Screenshot.png)

> **Walkthrough:** A complete walkthrough demonstrating dynamic filtering, agent drill-downs, and visual cross-highlighting is available in [PWC Report DashBoard VIdeo.mp4](PWC%20Report%20DashBoard%20VIdeo.mp4).

---

## Executive Summary & Business Objectives

PhoneNow (a telecom client partner of PwC) required an analytical overview of call center operations to shift from reactive management to data-driven decision-making. 

### Core Objectives:
- **Monitor Inbound Demand:** Identify peak calling windows and high-volume days to streamline agent workforce scheduling.
- **Evaluate Agent Productivity:** Benchmark individual agents across resolved rates, speed of answer, and average talk duration.
- **Diagnose Service Bottlenecks:** Quantify call abandonment rates and analyze the relationship between answer speed and customer satisfaction.
- **Track Customer Sentiment:** Measure overall CSAT distribution across distinct issue categories (e.g., Contract-related, Tech Support, Billing).

---

## Key Performance Indicators (KPIs)

| Metric | Business Definition | Formula / Implementation |
| :--- | :--- | :--- |
| **Total Inbound Calls** | Gross volume of incoming customer calls logged. | Count of distinct call records |
| **Answered Rate (%)** | Percentage of calls successfully connected to an agent. | $(Calls\ Answered / Total\ Calls) \times 100$ |
| **Abandonment Rate (%)** | Proportion of incoming callers who hung up before connecting. | $(Unanswered\ Calls / Total\ Calls) \times 100$ |
| **Resolution Rate (%)** | Percentage of answered calls where the query was resolved. | $(Resolved\ Calls / Calls\ Answered) \times 100$ |
| **Avg Speed of Answer (s)** | Average wait time in seconds before an agent picks up. | $\sum (Speed\ of\ Answer) / Calls\ Answered$ |
| **Average CSAT Score** | Overall customer satisfaction score on a 1–5 scale. | Mean rating of all scored post-call surveys |

---

## Data Pipeline & Architecture

### 1. Extraction & Cleaning (Power Query)
- **Schema Validation:** Ingested `01 Call-Center-Dataset.csv`, validating and mapping data types (Strings, Integers, Date/Time, and Durations).
- **Handling Missing Values:** Handled blank entries in `Speed of answer in seconds`, `AvgTalkDuration`, and unrated surveys in `Satisfaction rating` to prevent calculation bias.
- **Time Transformations:** Derived date dimension attributes (`Day Name`, `Hour of Day`, `Month`, `Weekday vs. Weekend`) from call timestamps to evaluate intraday trends.
- **Data Normalization:** Standardized text-based boolean indicators (`Y`/`N`) into consistent binary measures across resolution and answered statuses.

### 2. Analytical Data Modeling & DAX Measures
All business calculations are dynamically computed using custom DAX measures:

```dax
-- Total Inbound Call Volume
Total Calls = COUNT('Call-Center-Dataset'[Call Id])

-- Total Connected Calls
Calls Answered = 
CALCULATE(
    COUNT('Call-Center-Dataset'[Call Id]), 
    'Call-Center-Dataset'[Answered (Y/N)] = "Y"
)

-- Calls Unanswered / Abandoned
Calls Abandoned = 
CALCULATE(
    COUNT('Call-Center-Dataset'[Call Id]), 
    'Call-Center-Dataset'[Answered (Y/N)] = "N"
)

-- Overall Call Resolution Rate
Resolution Rate = 
DIVIDE(
    CALCULATE(COUNT('Call-Center-Dataset'[Call Id]), 'Call-Center-Dataset'[Resolved] = "Y"),
    [Calls Answered],
    0
)

-- Average Speed of Answer (Seconds)
Avg Speed of Answer = AVERAGE('Call-Center-Dataset'[Speed of answer in seconds])

-- Customer Satisfaction (CSAT) Average
Average CSAT = 
CALCULATE(
    AVERAGE('Call-Center-Dataset'[Satisfaction rating]),
    'Call-Center-Dataset'[Satisfaction rating] > 0
)
```

## Key Insights & Business Takeaways
1. Peak Inbound Hours: Incoming call volume surges between 10:00 AM and 2:00 PM. Deploying staggered shifts during     these midday hours will reduce call queues and lower abandonment rates.
2. Speed of Answer vs. CSAT: Callers experiencing answer speeds under 30 seconds consistently submit satisfaction      scores >= 4, whereas hold times exceeding 60 seconds exhibit a steep drop in positive sentiment.
3. Topic Concentration: Technical Support and Contract Admin account for the highest proportion of call volume and      talk time, pinpointing areas where self-service portal FAQs or automated IVR routing would yield maximum impact.
4. Agent Performance Variance: While individual resolution rates consistently sit above 70%, talk duration varies      significantly across agents for similar topics, suggesting opportunities for standardized call-handling training.

   
## Repository Structure

├── 01 Call-Center-Dataset.csv               # Raw customer call dataset
├── PWC Reports.pbix                         # Power BI Desktop report with data model & DAX
├── PWC Report Dashboard Screenshot.png      # Dashboard preview image
├── PWC Report DashBoard VIdeo.mp4           # Interactive dashboard walkthrough recording
├── Logo-pwc.png                             # PwC simulation branding asset
├── color palette.png                        # Report color palette reference
└── README.md                                # Project documentation

## Installation & Usage
1. Clone or download this repository:
  git clone [https://github.com/Anshulworld/PWC_Report_Dashboard_End_To_End_PowerBI_Project.git](https://github.com/Anshulworld/PWC_Report_Dashboard_End_To_End_PowerBI_Project.git)

3. Open PWC Reports.pbix in Power BI Desktop.

4. Data Source Configuration:

 a. If prompted by Power BI Desktop to refresh the data source, navigate to Home > Transform Data > Data Source         Settings.

 b. Update the file path to point to your local clone of 01 Call-Center-Dataset.csv.
