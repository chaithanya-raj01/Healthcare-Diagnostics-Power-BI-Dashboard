# 🏥 Dr. Reddy's Laboratories — Healthcare Diagnostic Dashboard
### End-to-End Business Intelligence Project | Power BI · Excel · DAX · Data Modeling

<br>

> **"Transforming raw diagnostic transaction data into executive-ready insights for one of India's leading pharmaceutical and diagnostics companies."**

---

## 📌 Table of Contents

1. [Project Overview](#-project-overview)
2. [Business Context](#-business-context)
3. [Problem Statement](#-problem-statement)
4. [Tech Stack](#-tech-stack)
5. [Data Architecture](#-data-architecture)
6. [Data Model (Star Schema)](#-data-model-star-schema)
7. [Data Cleaning & Transformation](#-data-cleaning--transformation)
8. [DAX Measures & KPIs](#-dax-measures--kpis)
9. [Dashboard Walkthrough](#-dashboard-walkthrough)
10. [Key Business Insights](#-key-business-insights)
11. [Skills Demonstrated](#-skills-demonstrated)
12. [Project Structure](#-project-structure)
13. [How to Use](#-how-to-use)
14. [Author](#-author)

---

## 🔍 Project Overview

This project delivers a **single-page executive Power BI dashboard** for **Dr. Reddy's Diagnostics**, enabling leadership and operations teams to monitor diagnostic center performance across cities and states — all in real time with drill-through interactivity.

The dashboard covers the full analytics lifecycle:
**Raw Excel Data → Data Cleaning → Star Schema Modeling → DAX Calculations → Interactive Dashboard**

| Attribute | Details |
|-----------|---------|
| **Domain** | Healthcare / Diagnostics |
| **Tool** | Microsoft Power BI Desktop |
| **Data Source** | Excel Workbook (multi-sheet) |
| **Dashboard Pages** | 1 (Diagnostic Executive Overview) |
| **Data Refresh** | Import Mode |
| **Target Audience** | C-Suite, Operations Managers, Regional Heads |

---

## 🏢 Business Context

**Dr. Reddy's Laboratories Ltd.** is a multinational pharmaceutical and healthcare company headquartered in Hyderabad, India. Its diagnostics division operates across multiple cities and states, offering a wide range of clinical tests through both in-person lab channels and digital/online booking channels.

As the diagnostic network scales, leadership needs a **centralized, real-time view** of:
- Revenue and profitability across test categories and geographies
- Turnaround Time (TAT) performance — a critical quality metric in diagnostics
- Customer acquisition and transaction volume trends
- Report delivery status (pending vs. completed)
- Channel-wise performance (Walk-In vs. Online vs. Partner)

This dashboard was built to answer those exact business questions.

---

## ❓ Problem Statement

> *"The operations and finance teams were working off disconnected Excel sheets, making it impossible to get a unified view of lab performance, test revenue, and turnaround efficiency across regions."*

**Key challenges addressed:**
- No single source of truth for diagnostic revenue and profitability
- TAT (Turnaround Time) was tracked manually — delays went unnoticed
- No visibility into which test categories or cities were underperforming
- Executive reporting took days instead of being available on demand

---

## 🛠 Tech Stack

| Layer | Tool |
|-------|------|
| Data Source | Microsoft Excel (.xlsx) |
| ETL / Transformation | Power Query (M Language) |
| Data Modeling | Power BI Desktop — Star Schema |
| Business Logic | DAX (Data Analysis Expressions) |
| Visualization | Power BI Desktop |
| Version Control | Git / GitHub |
| Theming | Custom Power BI Theme (JSON) |
| Branding | Dr. Reddy's Logo embedded in report |

---

## 🗂 Data Architecture

### Source: Excel Workbook (Multi-Sheet)

The raw dataset was structured across multiple sheets within an Excel workbook, representing different entities in the diagnostic business:

```
Excel Workbook
│
├── Fact_Transactions       ← Core transactional data (test bookings & results)
│     ├── Transaction_ID
│     ├── Test_Name
│     ├── Channel             (Walk-in / Online / Partner)
│     ├── Category
│     ├── Report_Status       (Completed / Pending)
│     ├── TAT_Hours           (Turnaround Time)
│     ├── Revenue
│     ├── Cost
│     └── Date
│
├── Dim_Tests               ← Test master data
│     ├── Test_Name
│     └── Category
│
├── Dim_Labs                ← Lab/center location master
│     ├── Lab_ID
│     ├── City
│     └── State
│
└── DateTable               ← Calendar dimension (Date hierarchy)
      ├── Date
      ├── Year / Quarter / Month / Day
```

### Data Flow

```
Excel (.xlsx)
     │
     ▼
Power Query (ETL)
  - Remove nulls & duplicates
  - Standardize column types
  - Rename & clean headers
  - Create calculated columns
     │
     ▼
Star Schema (Power BI Data Model)
  - Fact_Transactions (center)
  - Dim_Tests, Dim_Labs, DateTable (dimensions)
     │
     ▼
DAX Measures (Business KPIs)
     │
     ▼
Interactive Dashboard
```

---

## ⭐ Data Model (Star Schema)

The data model follows a **Star Schema** design — the industry standard for BI reporting — optimized for query performance and DAX simplicity.

```
                    ┌─────────────┐
                    │  DateTable  │
                    │─────────────│
                    │ Date (PK)   │
                    │ Year        │
                    │ Quarter     │
                    │ Month       │
                    └──────┬──────┘
                           │ 1:*
          ┌────────────────▼──────────────────┐
          │          Fact_Transactions         │
          │───────────────────────────────────│
┌─────────┤ Transaction_ID (PK)               ├─────────┐
│         │ Test_Name (FK → Dim_Tests)         │         │
│         │ Lab_ID (FK → Dim_Labs)             │         │
│         │ Date (FK → DateTable)              │         │
│         │ Channel                            │         │
│         │ Report_Status                      │         │
│         │ TAT_Hours                          │         │
│         │ Revenue                            │         │
│         └───────────────────────────────────┘         │
│  1:*                                            1:*    │
▼                                                        ▼
┌──────────────┐                              ┌──────────────┐
│  Dim_Tests   │                              │   Dim_Labs   │
│──────────────│                              │──────────────│
│ Test_Name PK │                              │ Lab_ID (PK)  │
│ Category     │                              │ City         │
└──────────────┘                              │ State        │
                                              └──────────────┘
```

**Design decisions:**
- `DateTable` was built using DAX `CALENDAR()` function for full date intelligence support
- All relationships are **one-to-many** from dimension to fact
- Cross-filter direction set to **Single** to avoid ambiguity in measures
- `Dim_Tests` and `Dim_Labs` are separate dimensions enabling independent slicing by test category and geography

---

## 🧹 Data Cleaning & Transformation

All transformations were performed in **Power Query Editor** before loading into the model:

| Step | Action Taken |
|------|-------------|
| Remove Duplicates | Identified and removed duplicate Transaction_IDs |
| Handle Nulls | Replaced null values in `TAT_Hours` with median; removed rows with null Revenue |
| Data Type Casting | Set correct types: Date, Decimal (Revenue/Cost), Whole Number (TAT), Text (Category) |
| Column Renaming | Standardized naming convention: `Snake_Case` for all columns |
| Derived Columns | Created `Profit = Revenue - Cost` in Power Query |
| Trim & Clean | Applied `Text.Trim()` and `Text.Clean()` on all text fields to remove whitespace |
| Filter Rows | Filtered out test/dummy transactions (non-production data) |
| Date Table | Auto-generated a full `DateTable` using DAX `CALENDARAUTO()` with Year, Quarter, Month, Day columns added via `ADDCOLUMNS` |

---

## 📐 DAX Measures & KPIs

All business KPIs are defined as **DAX measures** stored in a dedicated `Measures (2)` table for clean model organization.

```dax
-- Total Revenue
Total Revenue = SUM(Fact_Transactions[Revenue])

-- Total Profit
Total Profit = SUMX(Fact_Transactions, Fact_Transactions[Revenue] - Fact_Transactions[Cost])

-- Profit Margin %
Profit Margin % = DIVIDE([Total Profit], [Total Revenue], 0)

-- Total Transactions
Total Transactions = COUNTROWS(Fact_Transactions)

-- Total Customers
Total Customers = DISTINCTCOUNT(Fact_Transactions[Customer_ID])

-- Average Turnaround Time (TAT)
Avg TAT = AVERAGE(Fact_Transactions[TAT_Hours])

-- Revenue Trend (Time Intelligence)
Revenue Trend = CALCULATE([Total Revenue], DATESYTD(DateTable[Date]))
```

**DAX techniques used:**
- `SUMX` for row-by-row profit calculation
- `DIVIDE` for safe division (avoids divide-by-zero errors)
- `DISTINCTCOUNT` for unique customer count
- `DATESYTD` for Year-to-Date time intelligence
- `CALCULATE` + filter context manipulation
- `CALENDARAUTO` for dynamic date table generation

---

## 📊 Dashboard Walkthrough

### Page: Diagnostic Executive Overview

The single-page dashboard is designed for **executive consumption** — all critical metrics visible above the fold, with full drill-down capability.

#### 🔢 KPI Cards (Top Row)
| Card | Measure | Business Purpose |
|------|---------|-----------------|
| Total Revenue | `Total Revenue` | Overall revenue generated from all diagnostic tests |
| Total Profit | `Total Profit` | Net profitability after cost deduction |
| Total Transactions | `Total Transactions` | Volume of test bookings processed |
| Total Customers | `Total Customers` | Unique patient/customer count |
| Avg TAT | `Avg TAT` | Average turnaround time in hours — quality indicator |
| Profit Margin % | `Profit Margin %` | Operational efficiency ratio |

#### 📈 Charts & Visuals

| Visual Type | Title / Purpose | Fields Used |
|-------------|----------------|-------------|
| **Line Chart** | Revenue Trend over time | Revenue × Date Hierarchy (Year/Quarter/Month) |
| **Donut Chart** | Revenue split by Test Name | Revenue × Test_Name |
| **Clustered Bar Chart** | Revenue by Category | Revenue × Category |
| **Column Chart** | Transactions by Report Status | Transactions × Report_Status |
| **Pie Chart** | Revenue by Channel | Revenue × Channel (Walk-in/Online/Partner) |
| **Map Visual** | Geographic Revenue Distribution | Revenue × City × State |
| **Gauge** | TAT Performance vs. Target | Sum(TAT_Hours) vs. target threshold |

#### 🎛 Slicers (Filters)
| Slicer | Field | Purpose |
|--------|-------|---------|
| Date Hierarchy | Year / Quarter / Month / Day | Time period filter |
| Channel | Walk-in / Online / Partner | Booking channel filter |
| Category | Test category | Clinical category filter |
| City | Lab city | Location-based filter |
| State | Lab state | Regional filter |
| Test Name | Individual test | Specific test performance |

#### ✨ UX Features
- **"Clear All Slicers"** button — resets all filters in one click
- **Dr. Reddy's branding** — company logo embedded in report header
- **Custom theme** — aligned to Dr. Reddy's brand color palette
- **Map visual** — geographic revenue heatmap for regional analysis
- **Gauge visual** — TAT vs. SLA target with real-time status

---

## 💡 Key Business Insights

*(Derived from the dashboard design and data structure)*

1. **Revenue Concentration** — The donut chart reveals which specific tests drive the majority of revenue, enabling focused marketing and capacity planning.

2. **Channel Strategy** — The pie chart breaks down Walk-in vs. Online vs. Partner revenue, helping identify which acquisition channels deliver the highest ROI.

3. **TAT Compliance** — The gauge visual instantly flags whether labs are meeting Turnaround Time SLAs — a direct patient satisfaction driver.

4. **Report Backlog Risk** — The column chart showing Pending vs. Completed report status helps operations proactively manage workflow bottlenecks.

5. **Geographic Expansion** — The map visual identifies high-revenue cities and reveals states with growth potential.

6. **Profitability by Category** — The bar chart exposes which test categories have the highest margins vs. those requiring cost optimization.

---

## 🎯 Skills Demonstrated

| Skill Area | Specifics |
|-----------|-----------|
| **Data Modeling** | Star Schema design, dimension/fact table relationships, cardinality management |
| **Power Query (ETL)** | Data cleaning, null handling, type casting, text transformation, derived columns |
| **DAX** | Aggregations, CALCULATE, DIVIDE, DISTINCTCOUNT, DATESYTD, time intelligence |
| **Data Visualization** | KPI cards, line/bar/donut/pie/column charts, map visual, gauge, slicers |
| **UX Design** | Executive-focused layout, custom theme, branding, clear-all button |
| **Business Acumen** | Healthcare domain understanding, TAT/SLA concepts, profitability analysis |
| **Storytelling** | Single-page design that answers the "so what" for a C-suite audience |

---

## 📁 Project Structure

```
Dr-Reddys-Healthcare-Dashboard/
│
├── 📊 Dr_reddy_s__Health_care_Dashboard.pbix   ← Main Power BI report file
│
├── 📂 Data/
│   └── Healthcare_Raw_Data.xlsx                 ← Source Excel dataset
│
├── 📂 Screenshots/
│   ├── Dashboard_Overview.png                   ← Full dashboard screenshot
│   ├── KPI_Cards.png                            ← KPI card section
│   └── Data_Model.png                           ← Star schema screenshot
│
└── 📄 README.md                                 ← This file
```

> **Note:** Add your raw Excel file to the `Data/` folder and screenshots to `Screenshots/` before pushing to GitHub.

---

## ▶️ How to Use

### Prerequisites
- Microsoft Power BI Desktop (free) — [Download here](https://powerbi.microsoft.com/desktop/)
- The source Excel dataset placed in the `Data/` folder

### Steps

```bash
# 1. Clone this repository
git clone https://github.com/YOUR_USERNAME/Dr-Reddys-Healthcare-Dashboard.git

# 2. Open Power BI Desktop

# 3. Open the .pbix file
File → Open → Dr_reddy_s__Health_care_Dashboard.pbix

# 4. If prompted, update the data source path:
Home → Transform Data → Data Source Settings
→ Point to: Data/Healthcare_Raw_Data.xlsx

# 5. Refresh data
Home → Refresh

# 6. Explore the dashboard using slicers and visuals
```

---

## 👤 Author

**Chaithanya raj**
*| Data Analyst | Power BI Developer | Healthcare Analytics*

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-blue?style=flat&logo=linkedin)](https://linkedin.com/in/chaithanya-raj-veludandi-41b74821b/)
[![GitHub](https://img.shields.io/badge/GitHub-Follow-black?style=flat&logo=github)](https://github.com/chaithanya_01)
[![Email](https://img.shields.io/badge/Email-Contact-red?style=flat&logo=gmail)](mailto:chaithanyaraj.veludandi@gmail.com)

---

> ⭐ *If this project helped you or you found it useful, please consider giving it a star — it helps others discover it too!*

---

*Built with ❤️ using Power BI | Data tells a story — this dashboard tells Dr. Reddy's.*
