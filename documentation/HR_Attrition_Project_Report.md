# WORKFORCE RETENTION & HR ATTRITION ANALYTICS DASHBOARD
**End-to-End Business Intelligence Project**  
**Tool:** Microsoft Power BI Desktop  
**Dataset:** IBM HR Analytics Employee Attrition & Performance (1,470 Records, 35 Attributes)  

---

## 1. PROJECT OVERVIEW & EXECUTIVE SUMMARY

### 1.1 Business Problem Statement
High employee turnover (attrition) severely impacts organizational productivity, team morale, and operating profit. Replacing a salaried professional typically costs **1.5× to 2× their annual salary** due to recruitment agency fees, advertising, interviewing hours, onboarding, training, and lost project momentum.

The objective of this Power BI project is to analyze the historical workforce dataset of **1,470 employees**, diagnose the root causes behind employee resignations, uncover high-risk demographic and organizational segments, and deliver an interactive, executive-ready dashboard for HR leadership to take proactive retention interventions.

### 1.2 Key Project Metrics At A Glance
- **Total Employee Headcount:** 1,470
- **Total Attrition Count:** 237 employees separated
- **Active Employees:** 1,233 retained
- **Overall Corporate Attrition Rate:** **16.12%** (Industry benchmark is typically 12% - 15%)
- **Average Employee Age:** 36.9 Years
- **Average Monthly Income:** $6,503
- **Average Tenure at Company:** 7.0 Years
- **Total Monthly Payroll:** $9.56 Million

---

## 2. DATASET ARCHITECTURE & DATA DICTIONARY

The dataset contains employee demographics, job roles, compensation details, survey satisfaction ratings, and historical career trajectory:

| Category | Attributes | Description & Values |
| :--- | :--- | :--- |
| **Identifiers & Status** | `EmployeeNumber`, `Attrition` | Primary Key; Target variable: `Yes` (Left), `No` (Stayed) |
| **Demographics** | `Age`, `Gender`, `MaritalStatus`, `DistanceFromHome` | Age in years, Male/Female, Single/Married/Divorced, Commute distance in km |
| **Academic Background** | `Education`, `EducationField` | Level 1-5 (Below College to Doctor), Field: Life Sciences, Medical, Marketing, etc. |
| **Organizational Placement** | `Department`, `JobRole`, `JobLevel` | Department (Sales, R&D, HR); 9 distinct job roles; Hierarchy level 1 to 5 |
| **Compensation** | `MonthlyIncome`, `HourlyRate`, `DailyRate`, `MonthlyRate`, `PercentSalaryHike`, `StockOptionLevel` | Financial compensation figures, % increase in last review, stock grants |
| **Work Habits & Hours** | `OverTime`, `BusinessTravel`, `TotalWorkingYears` | Mandatory overtime (`Yes`/`No`), Travel frequency, Total career experience |
| **Tenure & Progression** | `YearsAtCompany`, `YearsInCurrentRole`, `YearsSinceLastPromotion`, `YearsWithCurrManager` | Time spent with the employer, role stagnation, promotion timeline |
| **Employee Surveys** | `JobSatisfaction`, `EnvironmentSatisfaction`, `RelationshipSatisfaction`, `WorkLifeBalance` | 1-4 Likert Scale: (1=Low/Bad, 2=Medium/Good, 3=High/Better, 4=Very High/Best) |

---

## 3. DATA PREPROCESSING & POWER QUERY (ETL)

Before building visualizations, raw data was cleaned and augmented in **Power Query Editor**:

### Step 1: Remove Redundant Constant Columns
The following columns contain only a single static value across all 1,470 rows and offer zero analytical variance:
- `EmployeeCount` (always `1`)
- `Over18` (always `'Y'`)
- `StandardHours` (always `80`)
*Action:* Select columns $\rightarrow$ Right Click $\rightarrow$ **Remove Columns**.

### Step 2: Feature Engineering & Bucket Creation
To create intuitive slicing dimensions, conditional/calculated columns were engineered:

1. **Attrition Numeric Flag:**
   ```powerquery
   if [Attrition] = "Yes" then 1 else 0
   ```
   *(Data Type: Whole Number `123`)*

2. **Age Group (Bins):**
   - `< 25`: Under 25 (Junior Cohort)
   - `25 - 34`: 25 to 34 (Core Professional)
   - `35 - 44`: 35 to 44 (Mid-Career)
   - `45 - 54`: 45 to 54 (Senior Professional)
   - `55+`: 55 and above (Veterans)

3. **Salary Slabs (Monthly Income):**
   - `Upto $3,000` (Entry level)
   - `$3,001 - $6,000` (Mid level)
   - `$6,001 - $10,000` (Senior)
   - `$10,001 - $15,000` (Principal / Director)
   - `Above $15,000` (Executive Leadership)

4. **Tenure Brackets (`YearsAtCompany`):**
   - `0 - 2 Years` (New Hires)
   - `3 - 5 Years` (Established)
   - `6 - 10 Years` (Tenured)
   - `10+ Years` (Veterans)

5. **Distance Group (`DistanceFromHome`):**
   - `Near (1 - 5 km)`
   - `Moderate (6 - 15 km)`
   - `Far (> 15 km)`

---

## 4. DAX MEASURES REPOSITORY

Create a dedicated disconnected table named `_Measures` to store all business logic cleanly:

### 4.1 Core Volume & Rate Measures
```dax
// Total Workforce
Total Employees = COUNTROWS('HR_Data')

// Attrition Volume
Attrition Count = 
CALCULATE(
    COUNTROWS('HR_Data'),
    'HR_Data'[Attrition] = "Yes"
)

// Retained Active Headcount
Active Employees = 
CALCULATE(
    COUNTROWS('HR_Data'),
    'HR_Data'[Attrition] = "No"
)

// Primary KPI: Attrition Rate
Attrition Rate = 
DIVIDE(
    [Attrition Count],
    [Total Employees],
    0
)

// Retention Rate
Retention Rate = 
DIVIDE(
    [Active Employees],
    [Total Employees],
    0
)
```

### 4.2 Gender & Demographics Measures
```dax
Male Employees = CALCULATE(COUNTROWS('HR_Data'), 'HR_Data'[Gender] = "Male")
Female Employees = CALCULATE(COUNTROWS('HR_Data'), 'HR_Data'[Gender] = "Female")

Male Attrition Rate = 
DIVIDE(
    CALCULATE([Attrition Count], 'HR_Data'[Gender] = "Male"),
    [Male Employees],
    0
)

Female Attrition Rate = 
DIVIDE(
    CALCULATE([Attrition Count], 'HR_Data'[Gender] = "Female"),
    [Female Employees],
    0
)

Average Age = AVERAGE('HR_Data'[Age])
Average Distance From Home = AVERAGE('HR_Data'[DistanceFromHome])
```

### 4.3 Compensation & Overtime Risk Measures
```dax
Average Monthly Income = AVERAGE('HR_Data'[MonthlyIncome])

// OverTime vs Non-OverTime Churn
OverTime Attrition Rate = 
DIVIDE(
    CALCULATE([Attrition Count], 'HR_Data'[OverTime] = "Yes"),
    CALCULATE(COUNTROWS('HR_Data'), 'HR_Data'[OverTime] = "Yes"),
    0
)

Non-OverTime Attrition Rate = 
DIVIDE(
    CALCULATE([Attrition Count], 'HR_Data'[OverTime] = "No"),
    CALCULATE(COUNTROWS('HR_Data'), 'HR_Data'[OverTime] = "No"),
    0
)
```

---

## 5. DASHBOARD ARCHITECTURE & VISUAL DESIGN

The dashboard is structured into **3 functional, interactive analytical pages**:

```
┌────────────────────────────────────────────────────────────────────────┐
│                   HR ATTRITION EXECUTIVE DASHBOARD                     │
├──────────────────┬─────────────────────────────┬───────────────────────┤
│ PAGE 1: OVERVIEW │ PAGE 2: DEMOGRAPHICS & EDU  │ PAGE 3: COMP & WLB    │
└──────────────────┴─────────────────────────────┴───────────────────────┘
```

---

### PAGE 1: EXECUTIVE OVERVIEW (THE HEADLINE COCKPIT)

**Objective:** Give the Chief Human Resources Officer (CHRO) an immediate high-level summary of headcount, turnover rate, department exposure, and critical role vulnerabilities.

#### 1. KPI Cards (Top Banner)
- **Card 1: Total Employees:** `[Total Employees]` $\rightarrow$ **1,470**
- **Card 2: Active Employees:** `[Active Employees]` $\rightarrow$ **1,233** (Formatted Green)
- **Card 3: Attrition Count:** `[Attrition Count]` $\rightarrow$ **237** (Formatted Crimson Red)
- **Card 4: Attrition Rate %:** `[Attrition Rate]` $\rightarrow$ **16.12%**
- **Card 5: Average Age:** `[Average Age]` $\rightarrow$ **36.9 Years**
- **Card 6: Average Monthly Income:** `[Average Monthly Income]` $\rightarrow$ **$6,503**

#### 2. Visual 1: Attrition Rate by Department
- **Visual Type:** Donut Chart or Horizontal Clustered Bar
- **Field Settings:**
  - `Department` $\rightarrow$ Legend / Y-axis
  - `[Attrition Rate]` $\rightarrow$ Values / X-axis
- **Output:**
  - **Sales:** **20.63%** (92 departed / 446 staff)
  - **Human Resources:** **19.05%** (12 departed / 63 staff)
  - **Research & Development:** **13.84%** (133 departed / 961 staff)

#### 3. Visual 2: The Overtime Smoking Gun
- **Visual Type:** 100% Stacked Bar Chart
- **Field Settings:**
  - `OverTime` $\rightarrow$ Y-axis (`Yes`, `No`)
  - `[Attrition Count]` & `[Active Employees]` $\rightarrow$ X-axis
- **Output:**
  - OverTime `Yes`: **30.53% Attrition**
  - OverTime `No`: **10.44% Attrition**
  - *Finding:* Employees required to work overtime leave at **300%** the rate of non-overtime staff.

#### 4. Visual 3: Attrition by Job Role (Risk Matrix)
- **Visual Type:** Clustered Bar Chart (Horizontal) sorted descending by Attrition Rate
- **Field Settings:**
  - `JobRole` $\rightarrow$ Y-axis
  - `[Attrition Rate]` $\rightarrow$ X-axis
  - `[Attrition Count]` $\rightarrow$ Tooltips
- **Output Ranking:**
  1. **Sales Representative:** **39.76%** *(Extreme Risk)*
  2. **Laboratory Technician:** **23.94%** *(High Risk)*
  3. **Human Resources:** **23.08%** *(High Risk)*
  4. **Sales Executive:** **17.48%**
  5. **Research Scientist:** **16.10%**
  6. **Manufacturing Director:** **6.90%**
  7. **Healthcare Representative:** **6.87%**
  8. **Manager:** **4.90%**
  9. **Research Director:** **2.50%** *(Most Stable)*

---

### PAGE 2: DEMOGRAPHICS & EDUCATION ANALYSIS

**Objective:** Understand personal, generational, and educational factors driving turnover.

#### 1. Visual 1: Attrition by Age Bracket
- **Visual Type:** Clustered Column Chart
- **Field Settings:**
  - `Age_Group` $\rightarrow$ X-axis (`< 25`, `25-34`, `35-44`, `45-54`, `55+`)
  - `[Attrition Rate]` $\rightarrow$ Y-axis
- **Key Output:**
  - `< 25 Years`: **39.18% Attrition** *(Nearly 4 in 10 young employees resign)*
  - `25 - 34 Years`: **19.79%**
  - `35 - 44 Years`: **10.12%**
  - `45 - 54 Years`: **10.20%**
  - `55+ Years`: **18.97%** *(Retirement / senior transitions)*

#### 2. Visual 2: Marital Status & Gender Cross-Tabulation
- **Visual Type:** Stacked Column Chart
- **Field Settings:**
  - `MaritalStatus` $\rightarrow$ X-axis (`Single`, `Married`, `Divorced`)
  - `Gender` $\rightarrow$ Legend
  - `[Attrition Rate]` $\rightarrow$ Y-axis
- **Key Output:**
  - **Single Employees:** **25.53% Attrition** (High mobility)
  - **Married Employees:** **12.48% Attrition**
  - **Divorced Employees:** **10.09% Attrition**

#### 3. Visual 3: Commute Distance vs Turnover
- **Visual Type:** Clustered Bar Chart
- **Field Settings:**
  - `Distance_Group` $\rightarrow$ Y-axis
  - `[Attrition Rate]` $\rightarrow$ X-axis
- **Key Output:**
  - `Near (1-5 km)`: **13.88%**
  - `Moderate (6-15 km)`: **15.06%**
  - `Far (> 15 km)`: **21.35%** (54% higher churn for long commuters)

---

### PAGE 3: COMPENSATION & WORK-LIFE BALANCE

**Objective:** Uncover how financial rewards and psychological well-being correlate with retention.

#### 1. Visual 1: Attrition by Monthly Income Slab
- **Visual Type:** Stacked Bar Chart
- **Field Settings:**
  - `Salary_Slab` $\rightarrow$ Y-axis
  - `[Attrition Rate]` $\rightarrow$ X-axis
- **Key Output:**
  - `Upto $3,000 / mo`: **28.84%** (Underpaid staff seek greener pastures)
  - `$3,001 - $6,000 / mo`: **14.77%**
  - `$6,001 - $10,000 / mo`: **8.94%**
  - `Above $15,000 / mo`: **7.69%**

#### 2. Visual 2: Job Satisfaction Rating vs Attrition
- **Visual Type:** Donut Chart or Treemap
- **Field Settings:**
  - `JobSatisfaction_Label` $\rightarrow$ Category / Legend
  - `[Attrition Count]` $\rightarrow$ Values
- **Key Output:**
  - Level 1 (Low): **22.84% Attrition**
  - Level 2 (Medium): **16.43%**
  - Level 3 (High): **16.52%**
  - Level 4 (Very High): **11.33% Attrition**

---

## 6. KEY ANALYTICAL FINDINGS & ROOT CAUSES

1. **The Overtime Catalyst:**
   Employees clocking mandatory overtime represent **53.6% of all resignations** despite constituting only 28.3% of the headcount. Their attrition rate is **30.5%** vs. **10.4%** for non-overtime peers.
2. **The Entry-Level Sales Trap:**
   Sales Representatives suffer an alarming **39.8% attrition rate**, plagued by a low average monthly salary ($2,626), high quota pressure, and frequent field travel.
3. **Early-Career Flight Risk:**
   Staff younger than 25 years exhibit a **39.2% attrition rate**. Young talent joins for experience but departs rapidly if clear mentorship and promotions are delayed.
4. **The Compensation Cliff:**
   Employees earning below **$3,000/month** churn at **28.8%**, while those earning above $6,000 churn at less than **9%**.
5. **Commute Fatigue:**
   Workers living farther than **15 km** from the office experience a **21.4% attrition rate** compared to **13.9%** for those living within 5 km.

---

## 7. STRATEGIC HR RECOMMENDATIONS & ACTION PLAN

| Priority | Risk Trigger | Recommended Strategic HR Initiative | Expected Impact |
| :---: | :--- | :--- | :--- |
| **P1** | **Chronic Overtime Burnout** | Implement strict quarterly overtime ceilings (max 10 hrs/week) and hire additional contract/temporary staff in high-volume laboratory and sales operations. | Reduce overtime attrition from 30.5% to < 18%. |
| **P2** | **Sales Rep Turnover (39.8%)** | Restructure the compensation model: elevate the base salary threshold to >$3,200/month and adjust commission triggers. Introduce a structured career pathway into senior "Sales Executive" roles within 18 months. | Save ~15 sales reps annually (~$750k replacement cost). |
| **P3** | **Junior Staff Flight (<25 yrs)** | Launch a 12-month **Early Career Mentorship Program** pairing new graduates with senior Managers and Directors. | Improve 1st-year retention by 25%. |
| **P4** | **Commute Fatigue (>15 km)** | Offer a **Hybrid Work Policy** (2-3 days work-from-home per week) or subsidized shuttle bus passes for employees residing outside the 15 km zone. | Moderate distance-related churn by ~30%. |

---

## 8. PROJECT ARTIFACT DIRECTORY

All assets for this project are locally accessible in:  
📁 `C:\Users\danes\.gemini\antigravity\scratch\hr_attrition_dashboard_project\`

- **Cleaned Dataset (CSV):** `data/HR_Employee_Attrition_Cleaned.csv`
- **Cleaned Dataset (Excel):** `data/HR_Employee_Attrition.xlsx`
- **DAX Formula Codebase:** `dax/All_DAX_Measures.dax`
- **Interactive Web Preview:** `dashboard_preview/HR_Attrition_Interactive_Dashboard.html`
- **Complete Project Report:** `documentation/HR_Attrition_Project_Report.md`
