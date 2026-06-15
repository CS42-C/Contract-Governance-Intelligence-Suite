# Contract Governance Intelligence Suite
## Complete DAX Measures Reference

---

## HOW TO USE THIS FILE

1. In Power BI Desktop, create a new table: Enter Data > name it `_Measures` > Load
2. Create each measure inside `_Measures` using New Measure
3. Measures are grouped by report page for clarity
4. Where USERELATIONSHIP is used, the inactive relationship must exist in your model

---

## PAGE 1 — CONTRACT OBLIGATIONS TRACKER

### Core Counts

```
Total Obligations =
COUNTROWS( Fact_Obligations )
```

```
Overdue Obligations =
CALCULATE(
    COUNTROWS( Fact_Obligations ),
    Fact_Obligations[Status] = "Overdue"
)
```

```
At Risk Obligations =
CALCULATE(
    COUNTROWS( Fact_Obligations ),
    Fact_Obligations[Status] = "At Risk"
)
```

```
On Track Obligations =
CALCULATE(
    COUNTROWS( Fact_Obligations ),
    Fact_Obligations[Status] = "On Track"
)
```

```
Completed Obligations =
CALCULATE(
    COUNTROWS( Fact_Obligations ),
    Fact_Obligations[Status] = "Completed"
)
```

```
In Progress Obligations =
CALCULATE(
    COUNTROWS( Fact_Obligations ),
    Fact_Obligations[Status] = "In Progress"
)
```

### Risk & Health Scores

```
Critical Overdue Obligations =
CALCULATE(
    COUNTROWS( Fact_Obligations ),
    Fact_Obligations[Status] = "Overdue",
    Fact_Obligations[Priority] = "Critical"
)
```

```
Obligation Compliance Rate =
DIVIDE(
    CALCULATE(
        COUNTROWS( Fact_Obligations ),
        Fact_Obligations[Status] IN { "On Track", "Completed", "In Progress" }
    ),
    [Total Obligations],
    0
)
```

```
Obligation Risk Score =
VAR OverdueWeight = 3
VAR AtRiskWeight = 2
VAR InProgressWeight = 1
VAR TotalWeighted =
    CALCULATE( COUNTROWS( Fact_Obligations ), Fact_Obligations[Status] = "Overdue" ) * OverdueWeight
    + CALCULATE( COUNTROWS( Fact_Obligations ), Fact_Obligations[Status] = "At Risk" ) * AtRiskWeight
    + CALCULATE( COUNTROWS( Fact_Obligations ), Fact_Obligations[Status] = "In Progress" ) * InProgressWeight
VAR MaxPossible = [Total Obligations] * OverdueWeight
RETURN
DIVIDE( TotalWeighted, MaxPossible, 0 )
```

```
Escalation Flag Count =
CALCULATE(
    COUNTROWS( Fact_Obligations ),
    Fact_Obligations[EscalationFlag] = TRUE()
)
```

```
Avg Completion Pct =
AVERAGE( Fact_Obligations[CompletionPct] )
```

### Priority Breakdown

```
Critical Obligations =
CALCULATE(
    COUNTROWS( Fact_Obligations ),
    Fact_Obligations[Priority] = "Critical"
)
```

```
High Priority Obligations =
CALCULATE(
    COUNTROWS( Fact_Obligations ),
    Fact_Obligations[Priority] = "High"
)
```

```
Overdue Rate =
DIVIDE(
    [Overdue Obligations],
    [Total Obligations],
    0
)
```

```
Overdue by Priority =
CALCULATE(
    COUNTROWS( Fact_Obligations ),
    Fact_Obligations[Status] = "Overdue",
    ALLEXCEPT( Fact_Obligations, Fact_Obligations[Priority] )
)
```

### Time Intelligence

```
Obligations Due This Week =
VAR TodayDate = TODAY()
VAR WeekStart = TodayDate - WEEKDAY( TodayDate, 2 ) + 1
VAR WeekEnd = WeekStart + 6
RETURN
CALCULATE(
    COUNTROWS( Fact_Obligations ),
    Fact_Obligations[DueDate] >= WeekStart,
    Fact_Obligations[DueDate] <= WeekEnd,
    Fact_Obligations[Status] <> "Completed"
)
```

```
Obligations Due Next 30 Days =
VAR TodayDate = TODAY()
RETURN
CALCULATE(
    COUNTROWS( Fact_Obligations ),
    Fact_Obligations[DueDate] >= TodayDate,
    Fact_Obligations[DueDate] <= TodayDate + 30,
    Fact_Obligations[Status] <> "Completed"
)
```

```
Obligations Overdue 30 Plus Days =
CALCULATE(
    COUNTROWS( Fact_Obligations ),
    Fact_Obligations[Status] = "Overdue",
    Fact_Obligations[DueDate] <= TODAY() - 30
)
```

---

## PAGE 2 — SUPPLIER CONTRACT COVERAGE MAP

### Core Counts

```
Total Coverage Records =
COUNTROWS( Fact_Coverage )
```

```
Signed Agreements =
CALCULATE(
    COUNTROWS( Fact_Coverage ),
    Fact_Coverage[CoverageStatus] = "Signed"
)
```

```
Missing Agreements =
CALCULATE(
    COUNTROWS( Fact_Coverage ),
    Fact_Coverage[CoverageStatus] = "Missing"
)
```

```
Pending Signature =
CALCULATE(
    COUNTROWS( Fact_Coverage ),
    Fact_Coverage[CoverageStatus] = "Pending Signature"
)
```

```
Expired Agreements =
CALCULATE(
    COUNTROWS( Fact_Coverage ),
    Fact_Coverage[CoverageStatus] = "Expired"
)
```

```
Under Review Agreements =
CALCULATE(
    COUNTROWS( Fact_Coverage ),
    Fact_Coverage[CoverageStatus] = "Under Review"
)
```

### Coverage Health

```
Coverage Rate =
DIVIDE(
    [Signed Agreements],
    [Total Coverage Records],
    0
)
```

```
Coverage Gap Rate =
DIVIDE(
    [Missing Agreements] + [Expired Agreements],
    [Total Coverage Records],
    0
)
```

```
Critical Coverage Gaps =
CALCULATE(
    COUNTROWS( Fact_Coverage ),
    Fact_Coverage[CoverageStatus] IN { "Missing", "Expired" }
)
```

```
Coverage Health Score =
VAR Signed = [Signed Agreements]
VAR Pending = [Pending Signature]
VAR UnderReview = [Under Review Agreements]
VAR Expired = [Expired Agreements]
VAR Missing = [Missing Agreements]
VAR Total = [Total Coverage Records]
VAR WeightedScore =
    ( Signed * 1.0 )
    + ( Pending * 0.5 )
    + ( UnderReview * 0.3 )
    + ( Expired * 0.1 )
    + ( Missing * 0.0 )
RETURN
DIVIDE( WeightedScore, Total, 0 )
```

### Expiry Intelligence

```
Agreements Expiring Next 90 Days =
VAR TodayDate = TODAY()
RETURN
CALCULATE(
    COUNTROWS( Fact_Coverage ),
    USERELATIONSHIP( Fact_Coverage[ExpiryDate], Dim_Date[Date] ),
    Fact_Coverage[ExpiryDate] >= TodayDate,
    Fact_Coverage[ExpiryDate] <= TodayDate + 90,
    Fact_Coverage[CoverageStatus] = "Signed"
)
```

```
Agreements Expiring Next 180 Days =
VAR TodayDate = TODAY()
RETURN
CALCULATE(
    COUNTROWS( Fact_Coverage ),
    USERELATIONSHIP( Fact_Coverage[ExpiryDate], Dim_Date[Date] ),
    Fact_Coverage[ExpiryDate] >= TodayDate,
    Fact_Coverage[ExpiryDate] <= TodayDate + 180,
    Fact_Coverage[CoverageStatus] = "Signed"
)
```

```
Days Since Last Review =
VAR LastReview =
    CALCULATE(
        MAX( Fact_Coverage[LastReviewedDate] )
    )
RETURN
DATEDIFF( LastReview, TODAY(), DAY )
```

---

## PAGE 3 — CLAUSE DEVIATION REGISTER

### Core Counts

```
Total Deviations =
COUNTROWS( Fact_Deviations )
```

```
Approved Deviations =
CALCULATE(
    COUNTROWS( Fact_Deviations ),
    Fact_Deviations[DeviationStatus] = "Approved"
)
```

```
Pending Approval Deviations =
CALCULATE(
    COUNTROWS( Fact_Deviations ),
    Fact_Deviations[DeviationStatus] = "Pending Approval"
)
```

```
Escalated Deviations =
CALCULATE(
    COUNTROWS( Fact_Deviations ),
    Fact_Deviations[DeviationStatus] = "Escalated"
)
```

```
Rejected Deviations =
CALCULATE(
    COUNTROWS( Fact_Deviations ),
    Fact_Deviations[DeviationStatus] = "Rejected"
)
```

```
High Risk Deviations =
CALCULATE(
    COUNTROWS( Fact_Deviations ),
    Fact_Deviations[RiskRating] = "High"
)
```

```
Recurring Deviations =
CALCULATE(
    COUNTROWS( Fact_Deviations ),
    Fact_Deviations[IsRecurring] = TRUE()
)
```

### Deviation Health & Patterns

```
Deviation Approval Rate =
DIVIDE(
    [Approved Deviations],
    [Total Deviations],
    0
)
```

```
High Risk Pending =
CALCULATE(
    COUNTROWS( Fact_Deviations ),
    Fact_Deviations[RiskRating] = "High",
    Fact_Deviations[DeviationStatus] IN { "Pending Approval", "Escalated", "Under Review" }
)
```

```
Avg Days To Resolve =
AVERAGE( Fact_Deviations[DaysToResolve] )
```

```
Recurring Rate =
DIVIDE(
    [Recurring Deviations],
    [Total Deviations],
    0
)
```

```
Deviation Risk Score =
VAR HighRisk =
    CALCULATE(
        COUNTROWS( Fact_Deviations ),
        Fact_Deviations[RiskRating] = "High",
        Fact_Deviations[DeviationStatus] <> "Approved"
    )
VAR MedRisk =
    CALCULATE(
        COUNTROWS( Fact_Deviations ),
        Fact_Deviations[RiskRating] = "Medium",
        Fact_Deviations[DeviationStatus] <> "Approved"
    )
VAR Total = [Total Deviations]
RETURN
DIVIDE( ( HighRisk * 3 ) + ( MedRisk * 1.5 ), Total * 3, 0 )
```

### Clause Analysis

```
Most Deviated Clause =
CALCULATE(
    FIRSTNONBLANK( Dim_Clause[ClauseName], 1 ),
    TOPN(
        1,
        SUMMARIZE(
            Fact_Deviations,
            Dim_Clause[ClauseName],
            "DevCount", COUNTROWS( Fact_Deviations )
        ),
        [DevCount],
        DESC
    )
)
```

```
Deviations Per Clause =
DIVIDE(
    [Total Deviations],
    DISTINCTCOUNT( Fact_Deviations[ClauseKey] ),
    0
)
```

```
Pending High Risk Escalation Rate =
DIVIDE(
    [High Risk Pending],
    [Total Deviations],
    0
)
```

---

## PAGE 4 — CONTRACT EXPIRY & RENEWAL PIPELINE

### Core Counts

```
Total Contracts =
COUNTROWS( Fact_Contracts )
```

```
Active Contracts =
CALCULATE(
    COUNTROWS( Fact_Contracts ),
    Fact_Contracts[ContractStatus] = "Active"
)
```

```
Total Contract Value =
SUM( Fact_Contracts[ContractValue_EUR] )
```

```
Active Contract Value =
CALCULATE(
    SUM( Fact_Contracts[ContractValue_EUR] ),
    Fact_Contracts[ContractStatus] = "Active"
)
```

### Expiry Windows

```
Expiring Within 30 Days =
CALCULATE(
    COUNTROWS( Fact_Contracts ),
    Fact_Contracts[DaysToExpiry] >= 0,
    Fact_Contracts[DaysToExpiry] <= 30,
    Fact_Contracts[ContractStatus] = "Active"
)
```

```
Expiring Within 90 Days =
CALCULATE(
    COUNTROWS( Fact_Contracts ),
    Fact_Contracts[DaysToExpiry] >= 0,
    Fact_Contracts[DaysToExpiry] <= 90,
    Fact_Contracts[ContractStatus] = "Active"
)
```

```
Expiring Within 180 Days =
CALCULATE(
    COUNTROWS( Fact_Contracts ),
    Fact_Contracts[DaysToExpiry] >= 0,
    Fact_Contracts[DaysToExpiry] <= 180,
    Fact_Contracts[ContractStatus] = "Active"
)
```

```
Value Expiring Within 90 Days =
CALCULATE(
    SUM( Fact_Contracts[ContractValue_EUR] ),
    Fact_Contracts[DaysToExpiry] >= 0,
    Fact_Contracts[DaysToExpiry] <= 90,
    Fact_Contracts[ContractStatus] = "Active"
)
```

```
Value Expiring Within 180 Days =
CALCULATE(
    SUM( Fact_Contracts[ContractValue_EUR] ),
    Fact_Contracts[DaysToExpiry] >= 0,
    Fact_Contracts[DaysToExpiry] <= 180,
    Fact_Contracts[ContractStatus] = "Active"
)
```

### Notice Period Urgency

```
Notice Deadline Passed =
CALCULATE(
    COUNTROWS( Fact_Contracts ),
    Fact_Contracts[DaysToNoticeDeadline] < 0,
    Fact_Contracts[ContractStatus] = "Active"
)
```

```
Notice Deadline Within 30 Days =
CALCULATE(
    COUNTROWS( Fact_Contracts ),
    Fact_Contracts[DaysToNoticeDeadline] >= 0,
    Fact_Contracts[DaysToNoticeDeadline] <= 30,
    Fact_Contracts[ContractStatus] = "Active"
)
```

```
Notice Deadline Within 60 Days =
CALCULATE(
    COUNTROWS( Fact_Contracts ),
    Fact_Contracts[DaysToNoticeDeadline] >= 0,
    Fact_Contracts[DaysToNoticeDeadline] <= 60,
    Fact_Contracts[ContractStatus] = "Active"
)
```

```
Value At Risk Notice Passed =
CALCULATE(
    SUM( Fact_Contracts[ContractValue_EUR] ),
    Fact_Contracts[DaysToNoticeDeadline] < 0,
    Fact_Contracts[ContractStatus] = "Active"
)
```

### Renewal Pipeline Health

```
Contracts Requiring Decision =
CALCULATE(
    COUNTROWS( Fact_Contracts ),
    Fact_Contracts[RenewalStatus] = "Decision Required"
)
```

```
Contracts Lapsed =
CALCULATE(
    COUNTROWS( Fact_Contracts ),
    Fact_Contracts[RenewalStatus] = "Lapsed"
)
```

```
Renewal Completion Rate =
DIVIDE(
    CALCULATE(
        COUNTROWS( Fact_Contracts ),
        Fact_Contracts[RenewalStatus] = "Renewal Completed"
    ),
    CALCULATE(
        COUNTROWS( Fact_Contracts ),
        Fact_Contracts[RenewalStatus] <> "No Action Required"
    ),
    0
)
```

```
Pipeline Health Score =
VAR Completed =
    CALCULATE(
        COUNTROWS( Fact_Contracts ),
        Fact_Contracts[RenewalStatus] = "Renewal Completed"
    )
VAR Initiated =
    CALCULATE(
        COUNTROWS( Fact_Contracts ),
        Fact_Contracts[RenewalStatus] = "Renewal Initiated"
    )
VAR DecisionRequired =
    CALCULATE(
        COUNTROWS( Fact_Contracts ),
        Fact_Contracts[RenewalStatus] = "Decision Required"
    )
VAR Lapsed =
    CALCULATE(
        COUNTROWS( Fact_Contracts ),
        Fact_Contracts[RenewalStatus] = "Lapsed"
    )
VAR NoticeOpen =
    CALCULATE(
        COUNTROWS( Fact_Contracts ),
        Fact_Contracts[RenewalStatus] = "Notice Period Open"
    )
VAR Total =
    CALCULATE(
        COUNTROWS( Fact_Contracts ),
        Fact_Contracts[RenewalStatus] <> "No Action Required"
    )
RETURN
DIVIDE(
    ( Completed * 1.0 ) + ( Initiated * 0.6 ) + ( NoticeOpen * 0.3 )
    - ( Lapsed * 1.0 ) - ( DecisionRequired * 0.5 ),
    Total,
    0
)
```

---

## SHARED MEASURES (used across multiple pages)

```
Selected Country =
IF(
    HASONEVALUE( Dim_Country[Country] ),
    VALUES( Dim_Country[Country] ),
    "All Countries"
)
```

```
Selected Client =
IF(
    HASONEVALUE( Dim_Client[ClientName] ),
    VALUES( Dim_Client[ClientName] ),
    "All Clients"
)
```

```
Contracts by Country =
CALCULATE(
    COUNTROWS( Dim_Contract ),
    ALLEXCEPT( Dim_Contract, Dim_Contract[CountryKey] )
)
```

```
Total Active Contract Value Formatted =
FORMAT( [Active Contract Value], "€#,##0,,\" M\"" )
```

```
Compliance Rate Label =
FORMAT( [Obligation Compliance Rate], "0.0%" )
```

```
Coverage Rate Label =
FORMAT( [Coverage Rate], "0.0%" )
```

```
Renewal Completion Rate Label =
FORMAT( [Renewal Completion Rate], "0.0%" )
```

---

## CONDITIONAL FORMATTING MEASURES
## Use these as background colour rules on tables and cards

```
Obligation Status Colour =
SWITCH(
    MAX( Fact_Obligations[Status] ),
    "Overdue", "#E24B4A",
    "At Risk", "#EF9F27",
    "In Progress", "#378ADD",
    "On Track", "#639922",
    "Completed", "#1D9E75",
    "#888780"
)
```

```
Coverage Status Colour =
SWITCH(
    MAX( Fact_Coverage[CoverageStatus] ),
    "Signed", "#639922",
    "Pending Signature", "#EF9F27",
    "Missing", "#E24B4A",
    "Expired", "#D85A30",
    "Under Review", "#378ADD",
    "#888780"
)
```

```
Deviation Risk Colour =
SWITCH(
    MAX( Fact_Deviations[RiskRating] ),
    "High", "#E24B4A",
    "Medium", "#EF9F27",
    "Low", "#639922",
    "#888780"
)
```

```
Renewal Status Colour =
SWITCH(
    MAX( Fact_Contracts[RenewalStatus] ),
    "Lapsed", "#E24B4A",
    "Decision Required", "#EF9F27",
    "Notice Period Open", "#EF9F27",
    "Renewal Initiated", "#378ADD",
    "Renewal Completed", "#639922",
    "No Action Required", "#888780",
    "#888780"
)
```

```
Days To Expiry Colour =
VAR Days = MIN( Fact_Contracts[DaysToExpiry] )
RETURN
IF( Days < 0, "#E24B4A",
IF( Days <= 30, "#D85A30",
IF( Days <= 90, "#EF9F27",
IF( Days <= 180, "#378ADD",
"#639922" ))))
```
