# 📊 HR Employee Attrition & Workforce Intelligence Dashboard

[![Power BI](https://img.shields.io/badge/Power_BI-Desktop-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)](https://powerbi.microsoft.com/)
[![DAX](https://img.shields.io/badge/DAX-Data_Analysis_Expressions-205493?style=for-the-badge)](https://learn.microsoft.com/en-us/dax/)
[![Power Query](https://img.shields.io/badge/Power_Query-ETL-0078D4?style=for-the-badge)](https://learn.microsoft.com/en-us/power-query/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg?style=for-the-badge)](https://opensource.org/licenses/MIT)

> An end-to-end People Analytics and Business Intelligence project analyzing employee turnover patterns, identifying flight-risk cohorts, diagnosing root causes of attrition, and providing HR leadership with an actionable retention strategy projected to save **$1.57 Million** annually.

---

## 📌 Executive Summary & Key Metrics

High turnover costs organizations between **1.5× to 2× an employee's annual salary** in direct recruitment, interview hours, lost velocity, and onboarding overhead. Analyzing an enterprise cohort of **1,470 employees**:

| Total Headcount | Active Retained | Attrition Count | Enterprise Attrition Rate | Average Age | Average Salary | Monthly Payroll |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **1,470** | **1,233** (83.9%) | **237** (16.1%) | **16.12%** | **36.9 Yrs** | **$6,503** | **$9.56M** |

```
Benchmark Comparison:
Our Corporate Attrition Rate:   [████████████████] 16.12%
Industry Healthy Benchmark:     [████████████░░░░] 12.00% - 15.00%
```

---

## 🗂️ Project Repository Structure

```
hr-attrition-dashboard-powerbi/
├── HR_Attrition_Dashboard.pbix            # Primary production Power BI Desktop dashboard
├── HR_Employee_Attrition_Model.pbix       # Alternative Star Schema analytics model
│
├── dashboard_preview/
│   └── HR_Attrition_Interactive_Dashboard.html # Full interactive browser mockup with tabs & slicers
│
├── data/
│   ├── HR_Employee_Attrition_Raw.csv      # Original 1,470 rows IBM HR dataset
│   ├── HR_Employee_Attrition_Cleaned.csv  # Feature-engineered dataset (Age bins, Salary slabs)
│   └── HR_Employee_Attrition.xlsx         # Formatted Microsoft Excel workbook
│
├── dax/
│   └── All_DAX_Measures.dax               # 25+ copy-paste ready DAX formulas with comments
│
├── documentation/
│   ├── HR_Attrition_Project_Explanation_Guide.docx # Official Word report & viva guide
│   ├── HR_Attrition_Project_Explanation_Guide.html # Printable styled report
│   ├── HR_Attrition_Project_Explanation_Guide.md   # Markdown technical architecture
│   ├── HR_Attrition_Project_Report.docx            # Executive report document
│   └── HR_Attrition_Project_Report.md              # Markdown executive report
│
└── README.md                              # Main GitHub documentation
```

---

## 🖥️ Dashboard Architecture & Visual Walkthrough

The Power BI Dashboard consists of **3 specialized analytical pages**:

### 1. Executive Cockpit (Page 1)
* **Headline KPI Scorecard:** Real-time visibility into Headcount, Retained staff, Attrition count, Attrition %, and Average Age.
* **Department Exposure (Donut / Bar):**
  * **Sales:** **20.63%** (92 departed / 446 staff) — *Highest relative risk*
  * **Human Resources:** **19.05%** (12 departed / 63 staff)
  * **R&D:** **13.84%** (133 departed / 961 staff)
* **The Overtime Impact (100% Stacked Bar):**
  * Staff working OverTime: **30.53% Attrition**
  * Staff with NO OverTime: **10.44% Attrition**
  * *Finding:* Mandatory overtime nearly **triples** turnover probability.
* **Job Role Risk Matrix (Ranked Horizontal Bar):**
  * 🚨 **Sales Representative:** **39.76%** *(Extreme risk)*
  * 🚨 **Laboratory Technician:** **23.94%** *(High risk)*
  * ⚠️ **Human Resources:** **23.08%**
  * 🟢 **Manager / Research Director:** **2.50% - 4.90%** *(Most stable)*

### 2. Demographics & Education (Page 2)
* **Age Bracket Analysis:** Under 25 years old suffer a critical **39.18% turnover rate**, dropping to **~10%** during prime career years (35-54).
* **Marital Status & Mobility:** Single employees have double the mobility (**25.53%**) of Married (**12.48%**) and Divorced (**10.09%**) peers.
* **Commute Distance Friction:** Employees commuting >15 km exhibit **21.35%** attrition vs. **13.88%** for those within 5 km.

### 3. Compensation & Employee Well-Being (Page 3)
* **The $3,000 Compensation Cliff:** Staff earning below $3,000/mo experience **28.84%** churn; once monthly salary crosses $6,000, churn drops below **9%**.
* **Job Satisfaction Dividend:** Employees rating satisfaction as "Very High" leave at half the frequency of those rating "Low" (**11.33% vs 22.84%**).

---

## 🔢 Core DAX Measures Repository

All measures are centralized inside a dedicated disconnected `_Measures` table:

```dax
// 1. Total Workforce Headcount
Total Employees = COUNTROWS('HR_Data')

// 2. Separated Employees (Attrition)
Attrition Count = 
CALCULATE(
    COUNTROWS('HR_Data'),
    'HR_Data'[Attrition] = "Yes"
)

// 3. Active Retained Headcount
Active Employees = 
CALCULATE(
    COUNTROWS('HR_Data'),
    'HR_Data'[Attrition] = "No"
)

// 4. Primary Business KPI: Attrition Rate
Attrition Rate = 
DIVIDE(
    [Attrition Count],
    [Total Employees],
    0
)

// 5. OverTime Churn Discrepancy
OverTime Attrition Rate = 
DIVIDE(
    CALCULATE([Attrition Count], 'HR_Data'[OverTime] = "Yes"),
    CALCULATE(COUNTROWS('HR_Data'), 'HR_Data'[OverTime] = "Yes"),
    0
)
```
*(See `dax/All_DAX_Measures.dax` for all 25+ formulas)*

---

## 💡 Strategic HR Recommendations & Projected ROI

| Priority | Risk Factor | Actionable HR Initiative | Target Outcome |
| :---: | :--- | :--- | :--- |
| **P1** | **Chronic Overtime (30.5%)** | Institute mandatory overtime caps (max 8 hrs/wk) and deploy flexible contractor support in fulfillment and lab operations. | Reduce overtime attrition to < 18%. |
| **P2** | **Sales Rep Crisis (39.8%)** | Raise base salary floor above $3,200/mo, adjust quota triggers, and create an 18-month advancement path into senior Sales Executive roles. | Retain ~15 sales reps annually (~$750k savings). |
| **P3** | **Junior Staff Bleed (<25 yrs)** | Launch a 12-month **Early Career Mentorship Program** pairing new graduates with experienced managers. | Improve 1st-year retention by 25%. |
| **P4** | **Commute Fatigue (>15 km)** | Introduce a **Hybrid Work Model** (2-3 days work-from-home) and public transit subsidies for workers living beyond 15 km. | Reduce distance-induced turnover by ~30%. |

> 💰 **Total Financial ROI:** Lowering corporate attrition from **16.12% to 13.00%** saves approximately **45 employees/year**, generating **$1,575,000 ($1.57 Million)** in recurring annual cost avoidance.

---

## 🚀 How to Run the Project Locally

1. **Clone the Repository:**
   ```bash
   git clone https://github.com/YOUR_USERNAME/hr-attrition-dashboard-powerbi.git
   cd hr-attrition-dashboard-powerbi
   ```
2. **Open in Power BI Desktop:**
   * Double-click `HR_Attrition_Dashboard.pbix` to launch the complete interactive report.
3. **Interactive Browser Preview:**
   * Open `dashboard_preview/HR_Attrition_Interactive_Dashboard.html` in any web browser to view the interactive dashboard mockup with working tabs and slicers.
4. **Documentation:**
   * Open `documentation/HR_Attrition_Project_Explanation_Guide.docx` for the comprehensive project report and viva voce interview guide.

---

## 👤 Author & Acknowledgements
* **Developer:** Dhanesh Nadesan
* **Tools:** Microsoft Power BI Desktop, DAX, Power Query, Microsoft Word
* **Dataset:** IBM HR Analytics Employee Attrition & Performance Dataset
* **License:** MIT License
