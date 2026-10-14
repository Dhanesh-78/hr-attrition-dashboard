# HR EMPLOYEE ATTRITION & WORKFORCE ANALYTICS DASHBOARD
## Comprehensive Project Explanation, Technical Architecture & Viva Guide

**Project Name:** HR Workforce Retention & Attrition Intelligence System  
**Business Domain:** Human Resources Management (HRM) / People Analytics  
**Primary Tool:** Microsoft Power BI Desktop & Service  
**Dataset Source:** IBM HR Analytics Employee Attrition & Performance Dataset  
**Target Audience:** HR Leadership (CHRO, VP of People), Department Heads, Business Analysts  

---

## 1. PROJECT OVERVIEW & THE ELEVATOR PITCH

### 1.1 The 30-Second Elevator Pitch (For Interviews & Quick Introductions)
> *"This project is an end-to-end People Analytics solution built in Power BI to analyze employee turnover across 1,470 staff members. By transforming raw demographic, compensation, and survey data through Power Query and DAX, the dashboard diagnosed that our overall attrition rate is 16.12%, with mandatory overtime, entry-level sales compensation, and commute distance being the primary churn drivers. It provides HR executives with interactive root-cause diagnostics and an actionable retention roadmap projected to save over $1.5 million in annual turnover costs."*

### 1.2 The 2-Minute Executive Summary
Employee turnover is one of the most expensive operational drains on modern corporations. Replacing an experienced employee costs between **1.5× to 2× their annual salary** when factoring in headhunter commissions, advertising, interviewing time, onboarding delays, and lost team productivity.

This project delivers a multi-page, interactive **Executive Power BI Dashboard** that transforms raw transactional employee records into actionable strategic intelligence. It enables HR leadership to:
1. **Monitor High-Level Vital Signs:** Real-time visibility into headcount, active retention rates, and payroll exposure.
2. **Pinpoint Vulnerable Cohorts:** Identify which departments, job roles, and age groups have dangerously elevated turnover.
3. **Isolate Root Causes:** Correlate attrition against work habits (overtime), financial tiers (monthly income slabs), and sentiment metrics (job satisfaction surveys).
4. **Drive Data-Backed Policy Decisions:** Formulate targeted interventions such as overtime caps, early-career mentorship programs, and hybrid commute policies.

---

## 2. BUSINESS CONTEXT & KEY PERFORMANCE INDICATORS (KPIs)

### 2.1 The Baseline Numbers
Across the enterprise cohort of **1,470 employees**:
- **Total Headcount:** 1,470
- **Total Attrition (Employees Left):** 237
- **Active Retained Employees:** 1,233 (Retention Rate: **83.88%**)
- **Enterprise Attrition Rate:** **16.12%** (Industry benchmark is typically 12% - 15%)
- **Average Employee Age:** 36.9 Years
- **Average Monthly Salary:** $6,503
- **Total Monthly Payroll:** $9,559,307 (~$9.56 Million)
- **Average Tenure at Company:** 7.0 Years

### 2.2 Departmental Exposure Summary
| Department | Total Staff | Attrition Count | Active Count | Attrition Rate % | Risk Assessment |
| :--- | :---: | :---: | :---: | :---: | :--- |
| **Sales** | 446 | 92 | 354 | **20.63%** | ⚠️ High Risk (1 in 5 leave) |
| **Human Resources** | 63 | 12 | 51 | **19.05%** | ⚠️ Elevated Risk |
| **Research & Development** | 961 | 133 | 828 | **13.84%** | ✅ Healthy / Within Benchmark |

---

## 3. DATASET ARCHITECTURE & DATA DICTIONARY

The underlying dataset comprises **1,470 employee records** and **35 distinct attributes** spanning four dimensions:

### 3.1 Dimension 1: Demographics & Personal Attributes
- `EmployeeNumber`: Unique employee identifier (Primary Key).
- `Age`: Employee chronological age (18 to 60 years).
- `Gender`: Male (882 / 60%) or Female (588 / 40%).
- `MaritalStatus`: Single, Married, or Divorced.
- `DistanceFromHome`: Commute distance in kilometers/miles (1 to 29 km).

### 3.2 Dimension 2: Academic & Organizational Placement
- `Education`: Formal education level (1: Below College, 2: College, 3: Bachelor, 4: Master, 5: Doctor).
- `EducationField`: Major subject (Life Sciences, Medical, Marketing, Technical Degree, Human Resources, Other).
- `Department`: Business division (Sales, R&D, Human Resources).
- `JobRole`: 9 granular organizational positions (e.g., Sales Rep, Lab Tech, Research Scientist, Manager).
- `JobLevel`: Internal hierarchical seniority band (1 to 5).

### 3.3 Dimension 3: Compensation & Working Conditions
- `MonthlyIncome`: Base monthly pay (Ranges from $1,009 to $19,999).
- `HourlyRate` / `DailyRate` / `MonthlyRate`: Secondary pay metrics.
- `PercentSalaryHike`: Annual percentage raise received (11% to 25%).
- `StockOptionLevel`: Equity grant tier (0 to 3).
- `OverTime`: Mandatory overtime requirement (`Yes` or `No`).
- `BusinessTravel`: Travel expectation (`Non-Travel`, `Travel_Rarely`, `Travel_Frequently`).

### 3.4 Dimension 4: Career Progression & Satisfaction Surveys
- `TotalWorkingYears`: Overall lifetime work experience.
- `YearsAtCompany`: Tenure with the current employer.
- `YearsInCurrentRole`: Time spent in current position without title change.
- `YearsSinceLastPromotion`: Career advancement stagnation tracker.
- `YearsWithCurrManager`: Relationship continuity with immediate supervisor.
- `JobSatisfaction`, `EnvironmentSatisfaction`, `RelationshipSatisfaction`, `WorkLifeBalance`: 1 to 4 Likert survey ratings (1=Low/Bad, 2=Medium/Good, 3=High/Better, 4=Very High/Best).
- `Attrition`: Target outcome variable (`Yes` if employee departed, `No` if retained).

---

## 4. DATA PREPROCESSING & POWER QUERY (ETL) PIPELINE

A production-grade Business Intelligence solution requires structured data cleansing before loading into the analytical engine:

### Step 1: Schema Sanitization & Zero-Variance Column Removal
Three columns in the raw IBM dataset contained identical, invariant values for all 1,470 rows:
- `EmployeeCount` = Always `1`
- `Over18` = Always `'Y'`
- `StandardHours` = Always `80`
*Action Taken:* Removed in Power Query to eliminate memory overhead and streamline the tabular schema.

### Step 2: Feature Engineering (Categorical Bins & Buckets)
Raw numerical columns can be difficult to interpret on dashboards. Meaningful categorical groupings were created:
1. **Age Bins (`Age_Group`):**
   - `< 25`: Under 25 (Junior Cohort)
   - `25 - 34`: 25 to 34 (Core Professional)
   - `35 - 44`: 35 to 44 (Mid-Career)
   - `45 - 54`: 45 to 54 (Senior Professional)
   - `55+`: 55 and Above (Veterans / Pre-Retirement)
2. **Salary Slabs (`Salary_Slab`):**
   - `Upto $3,000` (Entry-Level / High Risk)
   - `$3,001 - $6,000` (Mid-Level)
   - `$6,001 - $10,000` (Senior Staff)
   - `$10,001 - $15,000` (Leadership / Principal)
   - `Above $15,000` (Executive Band)
3. **Commute Distance Buckets (`Distance_Group`):**
   - `Near (1 - 5 km)`
   - `Moderate (6 - 15 km)`
   - `Far (> 15 km)`
4. **Tenure Brackets (`Tenure_Group`):**
   - `0 - 2 Years` (New Hires)
   - `3 - 5 Years` (Established Staff)
   - `6 - 10 Years` (Tenured Staff)
   - `10+ Years` (Veterans)

### Step 3: Human-Readable Label Translation
Survey metrics stored as cryptic integers (`1`, `2`, `3`, `4`) were mapped into clear descriptive labels:
- `1 - Low`, `2 - Medium`, `3 - High`, `4 - Very High` (for Job and Environment Satisfaction).
- `1 - Bad`, `2 - Good`, `3 - Better`, `4 - Best` (for Work-Life Balance).

---

## 5. DATA MODELING & DAX MEASURES ARCHITECTURE

### 5.1 Architecture & The Disconnected Measures Pattern
Instead of littering measures across transactional data tables, best practice was followed by creating a dedicated, disconnected table named `_Measures`. This centralizes all business calculations and makes maintenance seamless.

### 5.2 Key DAX Formulations Explained

#### 1. Total Headcount
```dax
Total Employees = COUNTROWS('HR_Data')
```
*Why COUNTROWS?* Evaluates the active row context faster than counting individual columns, accounting for active slicers.

#### 2. Attrition Count
```dax
Attrition Count = 
CALCULATE(
    COUNTROWS('HR_Data'),
    'HR_Data'[Attrition] = "Yes"
)
```
*Why CALCULATE?* Overrides the current filter context to filter specifically for separated staff (`Attrition = "Yes"`).

#### 3. Active Employees
```dax
Active Employees = 
CALCULATE(
    COUNTROWS('HR_Data'),
    'HR_Data'[Attrition] = "No"
)
```

#### 4. Primary Business KPI: Attrition Rate %
```dax
Attrition Rate = 
DIVIDE(
    [Attrition Count],
    [Total Employees],
    0
)
```
*Why DIVIDE()?* The safe division function prevents `#DIV/0!` errors when filters return zero employees, defaulting cleanly to `0`.

#### 5. OverTime Attrition Rate (The Risk Comparison)
```dax
OverTime Attrition Rate = 
DIVIDE(
    CALCULATE([Attrition Count], 'HR_Data'[OverTime] = "Yes"),
    CALCULATE(COUNTROWS('HR_Data'), 'HR_Data'[OverTime] = "Yes"),
    0
)
```

#### 6. Non-OverTime Attrition Rate
```dax
Non-OverTime Attrition Rate = 
DIVIDE(
    CALCULATE([Attrition Count], 'HR_Data'[OverTime] = "No"),
    CALCULATE(COUNTROWS('HR_Data'), 'HR_Data'[OverTime] = "No"),
    0
)
```

---

## 6. DASHBOARD WALKTHROUGH (PAGE-BY-PAGE ANALYSIS)

The Power BI dashboard is structured across **3 dedicated pages**, designed following visual hierarchy principles:

---

### PAGE 1: EXECUTIVE COCKPIT & OVERVIEW

**Target Audience:** Chief Executive Officer (CEO), Chief Human Resources Officer (CHRO).  
**Purpose:** Provide an instantaneous headline readout of company-wide retention health, departmental hot-spots, and critical role vulnerabilities.

1. **Top KPI Scorecard (6 Cards):**
   - **Total Employees (1,470):** Total cohort size.
   - **Active Employees (1,233):** Highlighted in green; shows strong overall baseline retention (83.88%).
   - **Attrition Count (237):** Highlighted in crimson; aggregate number of departures.
   - **Attrition Rate (16.12%):** Highlights that the firm is ~2% above standard corporate benchmark.
   - **Average Age (36.9 Years):** Indicates a mid-career, experienced workforce.
   - **Average Monthly Income ($6,503):** Baseline compensation metric.

2. **Visual: Departmental Churn (Donut / Bar Chart):**
   - Shows that **Sales** is the most volatile department at **20.63%**, closely followed by **Human Resources** at **19.05%**, while **R&D** remains relatively stable at **13.84%**.

3. **Visual: The OverTime Smoking Gun (Stacked Bar):**
   - Directly compares staff working mandatory overtime vs. normal hours.
   - **OverTime: Yes $ightarrow$ 30.53% Attrition**
   - **OverTime: No $ightarrow$ 10.44% Attrition**
   - *Interpretation:* Overtime nearly triples turnover probability.

4. **Visual: Job Role Vulnerability Ranking (Horizontal Bar Chart):**
   - Roles ranked descending by attrition rate:
     - 🚨 **Sales Representative:** **39.76%** (Extreme Churn)
     - 🚨 **Laboratory Technician:** **23.94%** (High Churn)
     - ⚠️ **Human Resources:** **23.08%**
     - ⚠️ **Sales Executive:** **17.48%**
     - ⚠️ **Research Scientist:** **16.10%**
     - 🟢 **Manufacturing Director:** **6.90%**
     - 🟢 **Healthcare Representative:** **6.87%**
     - 🟢 **Manager:** **4.90%**
     - 🟢 **Research Director:** **2.50%** (Most Stable)

---

### PAGE 2: DEMOGRAPHICS & EDUCATION ANALYSIS

**Target Audience:** HR Business Partners, Recruitment Directors.  
**Purpose:** Examine generational dynamics, mobility patterns, and academic correlation.

1. **Visual: Attrition by Age Bracket (Clustered Column Chart):**
   - Reveals an alarming **39.18% turnover rate** for employees **under 25 years old**.
   - Turnover stabilizes to **~10%** for prime working years (35 to 54 years).
   - Re-elevates to **18.97%** at age 55+ due to retirement transitions.

2. **Visual: Marital Status & Mobility Matrix (Stacked Column):**
   - **Single Employees:** **25.53% Attrition** (high mobility, fewer geographic ties).
   - **Married Employees:** **12.48% Attrition** (substantially higher stability).
   - **Divorced Employees:** **10.09% Attrition**.

3. **Visual: Commute Friction Analysis (Clustered Bar):**
   - Employees living within 5 km: **13.88% Attrition**.
   - Employees living farther than 15 km: **21.35% Attrition** (a 54% increase in departure risk due to commute fatigue).

---

### PAGE 3: COMPENSATION & WORK-LIFE WELL-BEING

**Target Audience:** Compensation & Benefits Committee, Operations Directors.  
**Purpose:** Uncover how compensation thresholds and psychological satisfaction impact retention.

1. **Visual: Monthly Salary Slab Distribution:**
   - **Upto $3,000 / month:** **28.84% Attrition** (The "Compensation Cliff").
   - **$3,001 - $6,000 / month:** **14.77% Attrition**.
   - **Above $6,000 / month:** Drops to **under 9%**.
   - *Key Takeaway:* Financial compensation is the predominant filter separating retained vs departing staff at junior levels.

2. **Visual: Job Satisfaction Rating vs Turnover:**
   - Employees reporting **Low Satisfaction (1)** exhibit a **22.84%** attrition rate.
   - Employees reporting **Very High Satisfaction (4)** exhibit an **11.33%** attrition rate.
   - *Satisfaction Dividend:* Happy employees leave at half the frequency of dissatisfied peers.

---

## 7. KEY DATA-DRIVEN INSIGHTS (ROOT CAUSE ANALYSIS)

Through exploratory analysis across the dashboard, **5 core root causes** of employee turnover were uncovered:

1. **The Overtime Burnout Engine:**
   Employees required to work overtime represent **53.6% of all corporate resignations**, despite making up only 28.3% of total headcount. Their turnover rate is **30.5% vs 10.4%**.
2. **The Entry-Level Sales Trap:**
   Sales Representatives endure the company's highest turnover (**39.8%**). They face an unforgiving triad of low base pay (averaging $2,626/mo), aggressive sales targets, and frequent field travel.
3. **Early-Career Talent Bleed:**
   Junior staff under 25 years old leave at **39.2%**. The organization attracts entry-level graduates but fails to engage or mentor them during their critical first 18 months.
4. **The $3,000 Compensation Cliff:**
   Nearly 3 in 10 employees earning below $3,000/month depart (**28.8%**), compared to fewer than 1 in 10 for those earning above $6,000.
5. **Commute Distance Fatigue:**
   Workers living farther than 15 km from the office depart at **21.4%**, indicating that uncompensated commute times negatively compound workplace stress.

---

## 8. STRATEGIC HR RECOMMENDATIONS & ACTION ROADMAP

| Priority | Targeted Vulnerability | Strategic Intervention | Projected Business Impact |
| :---: | :--- | :--- | :--- |
| **P1** | **Overtime Burnout (30.5%)** | Institute mandatory overtime ceilings (max 8 hrs/wk) and deploy flexible contractor support in laboratory and fulfillment operations. | Reduce overtime attrition from 30.5% to < 18%. |
| **P2** | **Sales Rep Turnover (39.8%)** | Adjust compensation structure: increase base pay to >$3,200/mo, re-align commission milestones, and create a structured 18-month advancement path into "Sales Executive". | Retain ~15 additional sales reps annually (~$750,000 replacement savings). |
| **P3** | **Young Talent Flight (<25 yrs)** | Launch a structured **Early Career Mentorship Program** pairing junior staff with senior Managers and Directors. | Improve 1st-year retention by 25%. |
| **P4** | **Commute Fatigue (>15 km)** | Introduce a **Hybrid Work Model** (2-3 days work-from-home) and corporate transit subsidies for employees residing beyond 15 km. | Reduce distance-induced turnover by ~30%. |

### Estimated Total Financial ROI:
By reducing corporate attrition from **16.12% down to 13.00%** (saving ~45 employees annually across the organization at an average replacement cost of $35,000 per employee), the organization stands to save **$1,575,000 ($1.57 Million) every year**.

---

## 9. VIVA VOCE & INTERVIEW QUESTIONS & ANSWERS (Q&A GUIDE)

When presenting this project to interviewers or academic evaluators, use the following model responses:

#### Q1. Why did you choose Microsoft Power BI over tools like Excel or Tableau for this project?
> *"Power BI was selected because of its seamless end-to-end stack: Power Query handles robust ETL and automated feature engineering, the VertiPaq tabular engine provides lightning-fast in-memory processing for 1,470+ records, and DAX enables dynamic context-sensitive calculations (such as real-time multi-slicer filtering across departments and overtime status). Furthermore, Power BI's enterprise governance and dashboard publishing capabilities make it the industry standard for corporate BI delivery."*

#### Q2. Why did you use the `DIVIDE()` DAX function instead of the simple division operator `/`?
> *"The division operator `/` in DAX produces a runtime error (`#DIV/0!`) if the denominator is zero or empty. The `DIVIDE()` function inherently tests for division by zero and provides a third argument for an alternate result (which I set to `0`). This guarantees dashboard stability when users apply restrictive slicers that return zero employees for a specific sub-group."*

#### Q3. What is the fundamental difference between a Calculated Column and a Measure in your project?
> *"A **Calculated Column** (such as `Age_Group` or `Salary_Slab`) is computed during data refresh and stored statically in the VertiPaq tabular database, taking up physical memory. It is evaluated row-by-row.  
> A **Measure** (such as `[Attrition Rate]` or `[Active Employees]`) consumes zero database storage. It is calculated dynamically on-the-fly depending on the user's active filter context (slicers, chart axes, cross-filtering)."*

#### Q4. Why did you create a separate `_Measures` table?
> *"Centralizing all business logic into a dedicated, disconnected `_Measures` table is a Microsoft Power BI architectural best practice. It keeps the model organized, decouples business metrics from underlying physical data tables, and ensures formulas are easy to locate and audit as the project scales."*

#### Q5. What was the single most surprising insight uncovered by this dashboard?
> *"The single strongest predictor of employee turnover was not salary or tenure, but **mandatory overtime**. Employees working overtime had an attrition rate of 30.53% compared to just 10.44% for peers without overtime—making overtime workers nearly 3× more likely to leave the organization."*

#### Q6. How would you deploy and maintain this project in an enterprise environment?
> *"In a corporate environment, this `.pbix` file would be published to the **Power BI Service** inside a dedicated Workspace. We would configure a scheduled daily or weekly data refresh via an On-Premises Data Gateway connected to the HRMS (e.g., Workday or SAP SuccessFactors), enforce Row-Level Security (RLS) so managers only view their departmental staff, and distribute the report via a packaged Power BI App."*

---

## 10. PROJECT ASSETS DIRECTORY REFERENCE

All deliverables for this project are accessible locally on your system:
- **Interactive Web Preview:** `dashboard_preview/HR_Attrition_Interactive_Dashboard.html`
- **Power BI Desktop Files:** `HR_Attrition_Dashboard.pbix` and `HR_Employee_Attrition_Model.pbix`
- **DAX Formula Codebase:** `dax/All_DAX_Measures.dax`
- **Cleaned Data (Excel & CSV):** `data/HR_Employee_Attrition_Cleaned.csv` & `HR_Employee_Attrition.xlsx`
- **Written Project Report:** `documentation/HR_Attrition_Project_Report.docx`
- **Comprehensive Project Guide:** `documentation/HR_Attrition_Project_Explanation_Guide.docx`
