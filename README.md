# Bank Loan Report: Financial Data Analysis (SQL + Power BI)

An end-to-end analysis of a bank's loan portfolio. The project defines lending KPIs in **SQL**, then presents them in a **3-page interactive Power BI dashboard** (Summary, Overview, Details) that tracks portfolio health, repayment performance, and borrower segments.

**Tools:** SQL (T-SQL / SQL Server) · Power BI · DAX

---

## Business Problem

The bank needs to monitor and assess its lending activity in order to:

- Track key loan metrics and how they change month over month (MoM)
- Separate **good loans** from **bad loans** to judge portfolio quality
- Find out where lending is concentrated: by geography, loan term, purpose, employment length and home ownership
- Support data-driven lending and risk decisions

The full brief is in [`Problem Statement BANK LOAN REPORT.docx`](./Problem%20Statement%20BANK%20LOAN%20REPORT.docx).

---

## Dataset

| | |
|---|---|
| **File** | [`financial_loan.csv`](./financial_loan.csv) |
| **Records** | 38,576 loan applications |
| **Columns** | 24 |
| **Period** | 1 Jan 2021 – 12 Dec 2021 (issue date) |
| **Granularity** | One row per loan |

**Key fields:** `id`, `address_state`, `emp_length`, `emp_title`, `grade`, `sub_grade`, `home_ownership`, `issue_date`, `loan_status`, `purpose`, `term`, `verification_status`, `annual_income`, `dti`, `installment`, `int_rate`, `loan_amount`, `total_payment`, `total_acc`

Field definitions are explained in [`TERMINOLOGIES BANK LOAN REPORT.docx`](./TERMINOLOGIES%20BANK%20LOAN%20REPORT.docx). Background on how banks grant and monitor loans is in [`Domain Knowledge BANK LOAN REPORT.docx`](./Domain%20Knowledge%20BANK%20LOAN%20REPORT.docx).

> **Note:** The data ends on 12 Dec 2021, so December is a partial month. MoM comparisons for December should be read with that in mind.

---

## Approach

1. **Understand the domain:** reviewed the loan lifecycle (application, credit and DTI checks, approval, disbursement, repayment, monitoring) and the meaning of each field.
2. **Load the data:** imported `financial_loan.csv` into a SQL table named `bank_loan_data`.
3. **Define KPIs in SQL:** wrote queries for the headline KPIs, with Month-to-Date (MTD) and Previous-Month-to-Date (PMTD) variants for MoM tracking. The queries are in [`BANK LOAN QUERY DOCUMENT.docx`](./BANK%20LOAN%20QUERY%20DOCUMENT.docx).
4. **Classify loan quality:**
   - **Good loan:** `loan_status` is `Fully Paid` or `Current`
   - **Bad loan:** `loan_status` is `Charged Off`
5. **Build the dashboard:** modelled the data in Power BI and built three report pages ([`bank loan dashboard.pbix`](./bank%20loan%20dashboard.pbix)).

---

## KPIs Tracked

| KPI | Definition |
|---|---|
| Total Loan Applications | `COUNT(id)` |
| Total Funded Amount | `SUM(loan_amount)` |
| Total Amount Received | `SUM(total_payment)` |
| Average Interest Rate | `AVG(int_rate) × 100` |
| Average DTI | `AVG(dti) × 100` |
| Good / Bad Loan % | Share of applications by loan quality class |
| Good / Bad Loan Funded and Received | Funded and repaid amounts per class |

Each headline KPI has an **MTD** and **PMTD** version, so the dashboard can show MoM change.

### Sample query

```sql
-- Good loan percentage
SELECT
    COUNT(CASE WHEN loan_status IN ('Fully Paid', 'Current') THEN id END) * 100.0
    / COUNT(id) AS Good_Loan_Percentage
FROM bank_loan_data;
```

---

## Dashboard

The Power BI report has three pages:

### 1. Summary
- KPI cards for applications, funded amount, amount received, average interest rate and average DTI, each with MTD and MoM change
- Good loan vs bad loan KPI panels
- **Loan Status grid view** showing applications, funded amount, amount received, interest rate and DTI by status

### 2. Overview
| Visual | Shows |
|---|---|
| Area chart | Monthly trend of applications, funded amount and amount received |
| Filled map | Lending by US state |
| Donut chart | Split by loan term (36 vs 60 months) |
| Bar chart | Employment length |
| Bar chart | Loan purpose |
| Tree map | Home ownership |

### 3. Details
A detailed, filterable table of loan-level records for drill-down.

<!-- Add screenshots here once exported from Power BI:
![Summary](images/summary.png)
![Overview](images/overview.png)
![Details](images/details.png)
-->

---

## Key Results

Figures below come from the full 2021 dataset.

**Portfolio snapshot**

| Metric | Value |
|---|---|
| Total loan applications | 38,576 |
| Total funded amount | $435.76M |
| Total amount received | $473.07M |
| Average interest rate | 12.05% |
| Average DTI | 13.33% |

**Loan quality**

| | Applications | Share | Funded | Received |
|---|---|---|---|---|
| Good loans (Fully Paid + Current) | 33,243 | 86.18% | $370.22M | $435.79M |
| Bad loans (Charged Off) | 5,333 | 13.82% | $65.53M | $37.28M |

**Latest month vs previous month (Dec vs Nov 2021, MTD)**

| Metric | Nov | Dec | MoM change |
|---|---|---|---|
| Loan applications | 4,035 | 4,314 | +6.9% |
| Funded amount | $47.75M | $53.98M | +13.0% |
| Amount received | $50.13M | $58.07M | +15.8% |

**Insights**

- **Healthy portfolio overall:** about 86% of loans are good. Bad loans are 13.8% of applications and 15.0% of the funded amount.
- **Weak recoveries on bad loans:** charged-off loans returned $37.28M against $65.53M funded, about 57% of the principal.
- **Longer terms are riskier:** 60-month loans have a charge-off rate of about 22.3%, compared with about 10.7% for 36-month loans.
- **Risk rises steadily by grade:** the charge-off rate climbs from about 5.7% for grade A to about 31.3% for grade G.
- **Demand is concentrated:** debt consolidation accounts for about 47% of all applications, and California alone accounts for about 18% of the funded amount.
- **Steady growth through 2021:** monthly applications rose from 2,332 in January to 4,314 in December.

---

## Repository Structure

```
├── README.md
├── financial_loan.csv                        # Raw dataset (38,576 loans)
├── BANK LOAN QUERY DOCUMENT.docx             # SQL queries for all KPIs and breakdowns
├── Problem Statement BANK LOAN REPORT.docx   # Business requirements for the 3 dashboards
├── Domain Knowledge BANK LOAN REPORT.docx    # Loan process and why banks analyse loan data
├── TERMINOLOGIES BANK LOAN REPORT.docx       # Data dictionary
└── bank loan dashboard.pbix                  # Power BI report (Summary, Overview, Details)
```

## How to Use

1. Clone the repo: `git clone https://github.com/arenapatra/Financial-Data-Analysis-Bank-Loan-.git`
2. Import `financial_loan.csv` into a SQL Server database as a table named `bank_loan_data`, then run the queries from the query document.
3. Open `bank loan dashboard.pbix` in **Power BI Desktop**. If prompted, point the data source to your local copy of `financial_loan.csv`.

## Skills Demonstrated

SQL aggregations and conditional logic · MTD / PMTD / MoM analysis · KPI design · Data modelling · Power BI dashboard design (cards, map, donut, tree map, area and bar charts, slicers) · Credit-risk and portfolio analysis

## Author

GitHub: [@arenapatra](https://github.com/arenapatra)
