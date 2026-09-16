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
























