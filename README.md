# NHS-Waiting-List-Patient-Flow-Analysis
Power BI project analysing synthetic NHS waiting list, patient flow and hospital capacity data, including long-wait trends, admissions, discharges, bed utilisation and readmissions.

## Project Overview

This project analyses a synthetic NHS-style dataset to explore waiting-list performance, patient flow, hospital capacity and long waiting times.

The objective was to build an interactive Power BI dashboard that allows users to monitor key operational indicators, compare hospitals and specialties, and identify areas of waiting-list pressure.

The final solution consists of three interactive dashboard pages:

- Executive Overview
- Patient Flow & Capacity
- Waiting List & Pathway Analysis

The dashboard includes interactive filters for Year, Hospital and Specialty.

## Business Problem

Healthcare organisations need to monitor waiting lists, patient flow and hospital capacity effectively.

When waiting-list and patient-flow information is analysed separately, it can be difficult to understand quickly:

- How large the current waiting-list backlog is
- How many pathways exceed 18 and 52 weeks
- How waiting lists change over time
- Which specialties experience greater waiting-list pressure
- Whether admissions and discharges are balanced
- How hospital capacity is being utilised
- The level of 30-day readmissions

A consolidated analytical view was therefore required to make these operational patterns easier to identify and investigate.

---

## Solution

I developed a three-page interactive Power BI dashboard that combines waiting-list, patient-flow and capacity analysis.

The solution includes:

- Executive-level KPI monitoring
- Waiting-list trend analysis
- 18-week and 52-week waiting measures
- Hospital and specialty comparisons
- Admissions and discharge analysis
- Length-of-stay analysis
- Bed-day utilisation
- Emergency admission analysis
- 30-day readmission analysis
- Interactive Year, Hospital and Specialty filters

The dashboard enables users to move from a high-level operational overview to more detailed waiting-list and patient-flow analysis.

---

## Dataset

The project uses synthetic NHS-style data covering waiting-list and patient-flow activity across multiple hospitals and specialties.

The model contains fact and dimension tables including:

Fact tables

- Fact_Waiting_List
- Fact_Patient_Flow

Dimension tables

- Dim_Date
- Dim_Patient
- Dim_Hospital
- Dim_Specialty

The data covers 2024–2025.

## Data Preparation

Before building the dashboard, the data was reviewed and prepared for analysis.

Key preparation steps included:

- Checking column names and data types
- Validating referral and treatment dates
- Identifying missing treatment dates
- Checking for invalid treatment dates occurring before referral dates
- Reviewing null values
- Creating and validating waiting-day fields
- Creating a dedicated date dimension
- Creating hospital and specialty dimensions
- Establishing relationships between fact and dimension tables
- Checking filter behaviour across the data model

A chronological sorting field was also created for Month-Year reporting:

```dax
YearMonthSort =
YEAR(Dim_Date[Date]) * 100
    + MONTH(Dim_Date[Date])
```
This ensures labels such as Jan 2024, Feb 2024 and Jan 2025 appear in the correct chronological order.

## Data Model
The Power BI model follows a fact-and-dimension structure.

Dimension tables provide filtering for:

Date → Hospital → Specialty

while the fact tables contain the waiting-list and patient-flow activity used by the dashboard measures.

This structure allows slicers to filter KPIs and visualisations across the report consistently.

## Key DAX Measures
```dax
Several DAX measures were created to support the analysis.

Current Waiting List
Current Waiting List =
CALCULATE(
    COUNTROWS(Fact_Waiting_List),
    Fact_Waiting_List[Pathway_Status] <> "Completed"
)
```

## Completed pathways are excluded so the KPI represents the active waiting-list backlog.
```dax
Patients Waiting Over 18 Weeks
Patients Waiting Over 18 Weeks =
CALCULATE(
    COUNTROWS(Fact_Waiting_List),
    Fact_Waiting_List[Pathway_Status] <> "Completed",
    Fact_Waiting_List[Waiting_Days] > 126
)
```

## % Waiting Over 18 Weeks
```dax
% Waiting Over 18 Weeks =
DIVIDE(
    [Patients Waiting Over 18 Weeks],
    [Current Waiting List],
    0
)
```

## Patients Waiting Over 52 Weeks
```dax
Patients Waiting Over 52 Weeks =
CALCULATE(
    COUNTROWS(Fact_Waiting_List),
    Fact_Waiting_List[Pathway_Status] <> "Completed",
    Fact_Waiting_List[Waiting_Days] > 364
)
```

## % Waiting Over 52 Weeks
```dax
% Waiting Over 52 Weeks =
COALESCE(
    DIVIDE(
        [Patients Waiting Over 52 Weeks],
        [Current Waiting List],
        0
    ),
    0
)
```

COALESCE was used so filter combinations with no qualifying 52+ week records display 0% instead of a blank value.

Additional measures were created for:

Total Admissions
Total Discharges
Unique Patients
Average Length of Stay
Total Bed Days
Current Average Waiting Days
30-Day Readmissions
30-Day Readmission Rate
Emergency Admissions

# Dashboard
## Page 1 — Executive Overview

The Executive Overview provides a high-level view of NHS-style waiting-list and hospital activity.

KPIs
- Total Admissions
- Unique Patients
- Current Waiting List
- Current Average Waiting Days
- % Waiting Over 18 Weeks
- % Waiting Over 52 Weeks
  
Visualisations
- Current Waiting List by Month
- Total Admissions by Admission Type
- Current Waiting List by Specialty
- Total Admissions by Hospital
- Current Waiting List by Waiting Band
- 52+ Week Wait Rate by Specialty

Interactive slicers allow analysis by:

Year | Hospital | Specialty

## Page 2 - Patient Flow and Capacity

This page focuses on operational patient flow and hospital capacity.

KPIs
- Total Admissions
- Total Discharges
- Average length of stay
- Total bed days
- 30-day readmission
- % Readmission rate

Visualisation
- Total Admissions and Total Discharges by Month
- Average Length of Stay by Hospital
- Total Bed Days by Hospital
- 30-Day Readmissions by Hospital
- Emergency Admissions by Hospital
- Total Discharges by Discharge Status

## Page 3 — Waiting List & Pathway Analysis

This page provides deeper analysis of the waiting-list backlog.

KPIs
- Current Waiting List
- Current Average Waiting Days
- Patients Waiting Over 18 Weeks
- % Waiting Over 18 Weeks
- Patients Waiting Over 52 Weeks
- % Waiting Over 52 Weeks

Visualisations
- Current Waiting List by Month
- Current Waiting List by Specialty
- Waiting Over 18 Weeks by Specialty
- Waiting Over 52 Weeks by Specialty
- Pathways by Status
- Current Waiting List by Waiting Band
  
# Key Findings
1. Significant long-wait backlog
- About 73.4% of the current waiting list exceeded 18 weeks, and about 41.9% exceeded 52 weeks.
This indicates that long-wait pathways represent a substantial proportion of the active backlog.

2. Backlog increased toward the end of the period
- Monthly analysis showed fluctuations throughout the reporting period, with higher waiting-list volumes appearing toward the later months of 2025.

3. Waiting pressure spans multiple specialties
- Waiting-list volumes and long-wait pathways were distributed across several specialties rather than being overwhelmingly concentrated in one clinical area.

4. Emergency admissions represent the largest admission type
- Of approximately 15K admissions, Emergency admissions formed the largest category, ahead of Elective and Day Case activity.

5. Admissions and discharges were broadly balanced
- Approximately 15K admissions and 15K discharges were recorded.
- Monthly admission and discharge trends also remained closely aligned across the reporting period.

6. Approximately 52K bed days were recorded
- Average Length of Stay was approximately 3.4 days, generating around 52K total bed days.
- Hospital-level length-of-stay and bed-day measures were relatively similar across the hospitals in the synthetic dataset.

7. 30-day readmission rate was approximately 9.3%
- Approximately 1K 30-day readmissions were recorded, corresponding to a 9.3% readmission rate.
- Hospital-level readmission volumes were relatively evenly distributed.

8. Home was the dominant discharge destination
- The majority of recorded discharges resulted in patients returning Home, while Community Care, Transfers and other outcomes represented smaller shares.

## Overall Analysis

The analysis identified long waiting times as the most prominent operational issue within the synthetic dataset.

Although admissions and discharges remained broadly balanced and hospital-level capacity measures were relatively consistent, a substantial proportion of the active waiting list exceeded both the 18-week and 52-week thresholds.

The analysis also showed increasing waiting-list pressure toward the end of the reporting period, highlighting the importance of monitoring long-wait pathways across multiple specialties.

Skills Demonstrated

This project demonstrates practical experience with:

Power BI

Interactive dashboard development
KPI cards
Line, bar, column and donut charts
Slicers and filter interactions
Visual formatting
Drill-down analysis

## DAX
1. CALCULATE
2. COUNTROWS
DIVIDE
COALESCE
Filter context
Conditional calculations
Time-based sorting

## Data Modelling
Fact and dimension tables
Date dimensions
Table relationships
Filter propagation
Star-schema concepts

## Data Analysis
Waiting-list analysis
Patient-flow analysis
Hospital performance comparison
Trend analysis
Capacity analysis
Readmission analysis
KPI validation

## Data Quality
Missing-value checks
Date validation
Data-type validation
Business-rule validation
Measure validation








































