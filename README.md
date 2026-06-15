# Contract Governance Intelligence Suite

A Power BI portfolio project built at the intersection of legal reasoning and data analytics.

Most organisations manage contracts reactively. Obligations are tracked in spreadsheets. Coverage gaps are discovered during audits. Clause deviations accumulate unnoticed. Renewal deadlines pass without action.

This dashboard was built to change that. Four pages. One data model. Complete contract governance visibility across a multi-jurisdiction portfolio.

---

## Portfolio at a glance

| Metric | Value |
|---|---|
| Obligations tracked | 194 |
| Compliance rate | 83.0% |
| Supplier coverage rate | 51.7% |
| Deviations logged | 180 |
| Pipeline health score | 43.6% |
| Contract value past notice deadline | €46.0M |

---

## Dashboard pages

### Page 1 — Obligation Tracker
Tracks all contractual obligations by status, priority, owner, and due date. Surfaces overdue and at-risk items with escalation flags, and breaks down patterns by obligation type.

Key findings: 24 overdue obligations, 9 at risk, 2 critical-priority obligations overdue. Governance meetings and deviation approvals account for the highest overdue volume by type.

### Page 2 — Supplier Coverage Map
Maps signed agreements across the full supplier-client matrix by country. Identifies missing, expired, and pending-signature gaps with full action detail.

Key findings: 51.7% overall coverage rate, 11 critical gaps (6 missing, 5 expired), zero coverage in Spain across all supplier-client pairs — a direct legal and commercial risk for every operational relationship in that jurisdiction.

### Page 3 — Clause Deviation Register
Tracks all clause deviations by type, risk rating, approval status, and recurrence. Identifies the most frequently negotiated clauses and measures resolution time.

Key findings: 20 high-risk deviations pending approval, 21 escalated above team lead, 26.1% recurring rate (47 recurring patterns), average resolution time of 46.7 days. Payment Terms and Confidentiality are the most deviated clauses.

### Page 4 — Renewal Pipeline
Monitors the full contract expiry horizon across 30, 90, and 180-day windows. Tracks notice deadlines separately from expiry dates, and scores overall pipeline health.

Key findings: 14 contracts past their notice deadline with €46M in value at stake, 2 decisions required (€27.1M), 9 contracts expiring within 90 days, pipeline health score of 43.6%.

---

## Data model

Star schema with 4 fact tables and 7 dimension tables.

**Fact tables**
- `Fact_Obligations` — obligation records with status, priority, due dates, and escalation flags
- `Fact_Coverage` — supplier-client agreement coverage by country and agreement type
- `Fact_Deviations` — clause deviation log with risk ratings, approval status, and recurrence tracking
- `Fact_Contracts` — contract master with values, expiry dates, notice deadlines, and renewal status

**Dimension tables**
- `Dim_Date` — full calendar table supporting time intelligence
- `Dim_Country` — country codes mapped to names and regions
- `Dim_Client` — client reference with sector and jurisdiction
- `Dim_Contract` — contract identifiers and metadata
- `Dim_Clause` — clause type reference with risk classifications
- `Dim_Supplier` — supplier reference data
- `Dim_Team` — team ownership reference

---

## DAX measures

60+ measures written from scratch across four functional areas. See [`DAX_Measures_Contract_Governance.md`](./DAX_Measures_Contract_Governance.md) for the full library.

Measure categories include:

- Core counts and status breakdowns per module
- Weighted composite scores: Obligation Risk Score, Coverage Health Score, Deviation Risk Score, Pipeline Health Score
- Time intelligence: obligations due this week, next 30 days, overdue 30+ days; agreements expiring within 90 and 180 days
- Notice period urgency: days to notice deadline, value at risk by notice status
- Conditional formatting measures: colour rules for obligation status, coverage status, deviation risk, renewal status, and days to expiry

---

## Technical stack

| Component | Detail |
|---|---|
| Visualisation | Power BI Desktop |
| Data modelling | Star schema, inactive relationships via USERELATIONSHIP |
| DAX | 60+ measures including weighted scores, time intelligence, and conditional formatting |
| Dataset generation | Python (pandas) |
| Theme | Custom JSON — dark navy palette, semantic colour system |
| Dataset scope | 10 countries, 10 clients, 15 suppliers, 50 contracts, 700+ rows across all tables |

---

## Repository contents

```
├── README.md
├── Contract_Governance_Suite.pbix
├── DAX_Measures_Contract_Governance.md
├── ContractGovernance_Theme_v2.json
├── Contract_Governance_Suite_v2_final2.xlsx
├── Report + Dashboard/
│   └── Contract Governance Suite.pdf
```

---

## About this project

The Contract Governance Intelligence Suite is a personal portfolio project demonstrating what happens when legal reasoning and analytical capability work together in one tool.

The dataset is synthetic but realistic, modelled on a multi-jurisdiction European facilities management portfolio. The data model, all DAX, and the custom theme were built from scratch.

The project sits at the core of my professional positioning: a Civil Law background that shapes how I interpret what the data means legally, and an analytics skill set that determines how I surface it.

---

**Francisco Costa**
[LinkedIn](https://linkedin.com/in/francscosta) · [GitHub](https://github.com/CS42-C) · franciscostabusiness@gmail.com