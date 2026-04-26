# HR Dashboard — MySQL + Power BI

> **HR workforce analytics for 22,000+ employee records (2000–2020), covering diversity, turnover, age distribution, and headcount trends.**

![HR Dashboard](https://user-images.githubusercontent.com/56026296/229609893-b7b1f261-5941-45af-8322-1ccb2535d36b.png)

---

## Overview

This project analyzes a 20-year HR dataset using MySQL for data cleaning and querying, and Power BI for visualization. It answers 11 business questions about workforce composition, helping identify diversity gaps, retention risks, and growth patterns.

---

## Dataset

| Property | Detail |
|---|---|
| **File** | `Human Resources.csv` |
| **Rows** | ~22,000+ employee records |
| **Period** | 2000 – 2020 |
| **Key columns** | `emp_id`, `first_name`, `last_name`, `birthdate`, `gender`, `race`, `department`, `jobtitle`, `location`, `hire_date`, `termdate`, `location_city`, `location_state` |

---

## Tools

| Tool | Purpose |
|---|---|
| **MySQL Workbench** | Data cleaning, type conversions, derived columns |
| **Jupyter Notebook + ipython-sql** | Interactive SQL querying with `%%sql` magic |
| **Power BI Desktop** | Dashboard and visual report creation |

---

## Project Structure

```
HR-Dashboard-MySQL-PowerBI/
├── Human Resources.csv      # Raw dataset
├── HR Data Cleaning.sql     # Schema fixes, date normalization, age column
├── HR Data Questions.sql    # 11 business questions in plain SQL
├── HR Final Queries.ipynb   # Full walkthrough: cleaning + analysis in Jupyter
├── HR Dashboard.pbix        # Power BI main dashboard
├── HR Employee Report.pbix  # Supplementary employee-level report
└── HR Dashboard.pdf         # Exported dashboard PDF
```

---

## Data Cleaning Steps

The raw CSV had inconsistent date formats and encoding issues. The SQL pipeline (`HR Data Cleaning.sql`) handles:

1. **Column rename** — fixes UTF-8 BOM artifact (`ï»¿id` → `emp_id`)
2. **Date normalization** — `birthdate` and `hire_date` stored as strings in mixed `MM/DD/YYYY` and `MM-DD-YYYY` formats; converted to `DATE` type via `STR_TO_DATE` + `DATE_FORMAT`
3. **Termdate cleanup** — strips UTC timestamp suffix, converts to `DATE`
4. **Age derivation** — `age = TIMESTAMPDIFF(YEAR, birthdate, CURDATE())`
5. **Data quality checks** — excludes 967 records with negative ages; excludes 1,599 future termdates from active analysis

---

## Analysis Questions

| # | Question |
|---|---|
| 1 | What is the gender breakdown of employees? |
| 2 | What is the race/ethnicity breakdown? |
| 3 | What is the age distribution? |
| 4 | Headquarters vs. remote employee split? |
| 5 | Average length of employment for terminated employees? |
| 6 | Gender distribution across departments and job titles? |
| 7 | Distribution of job titles? |
| 8 | Which department has the highest turnover rate? |
| 9 | Distribution of employees across states? |
| 10 | How has employee count changed over time? |
| 11 | What is the tenure distribution per department? |

---

## Key Findings

- **Gender**: More male employees than female across all departments; distribution is relatively balanced within departments.
- **Race**: White employees are the largest group; Native Hawaiian and American Indian employees are least represented.
- **Age**: Employees range from 20 to 57 years old. The 25–34 and 35–44 brackets are the largest; 55–64 is the smallest.
- **Location**: Most employees work at headquarters; remote workers are a minority.
- **Tenure**: Average employment for terminated staff is ~7 years. Average per-department tenure is ~8 years. Legal and Auditing have the highest tenure; Sales, Services, and Marketing the lowest.
- **Turnover**: Marketing has the highest turnover rate, followed by Training. Research & Development, Support, and Legal have the lowest.
- **Geography**: Ohio is the top state for employee location.
- **Headcount growth**: Net employee count has grown consistently year-over-year from 2000 to 2020.

---

## Setup & Reproduction

### Prerequisites

- MySQL 8.0+ (or MySQL Workbench)
- Python 3.8+
- Power BI Desktop (Windows)

### 1. Load the data into MySQL

```sql
CREATE DATABASE projects;
USE projects;
-- Import Human Resources.csv via MySQL Workbench Table Data Import Wizard
-- or: LOAD DATA INFILE '/path/to/Human Resources.csv' INTO TABLE hr ...
```

### 2. Run the cleaning script

```sql
SOURCE HR Data Cleaning.sql;
```

### 3. Run the Jupyter notebook

```bash
pip install jupyter ipython-sql pymysql
jupyter notebook "HR Final Queries.ipynb"
```

Update the connection string in Cell 1:

```python
%sql mysql+pymysql://root:<password>@localhost/projects
```

### 4. Open Power BI dashboards

Open `HR Dashboard.pbix` or `HR Employee Report.pbix` in Power BI Desktop and refresh the data source to point to your local MySQL instance.

---

## Limitations

- **967 records** with negative calculated ages were excluded (data entry errors in `birthdate`).
- **1,599 records** with `termdate` values beyond the current date were excluded from termination analysis.
- All age-based queries filter `age >= 18` to remove underage anomalies.
- Power BI files require a live MySQL connection; exported PDF (`HR Dashboard.pdf`) shows a static snapshot.
