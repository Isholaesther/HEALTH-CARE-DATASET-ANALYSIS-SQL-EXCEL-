# Healthcare Dataset Analysis (SQL Server)

## Project Overview
This project provides an end-to-end SQL analysis of a healthcare dataset, focusing on patients, hospitals, doctors, admissions, treatments, and billing.  
Using SQL Server for queries and Excel for visualizations, 15 business and clinical questions were answered, offering insights into patient demographics, medical conditions, test outcomes, length of stay, and financial trends.  
The findings highlight patterns that can support healthcare decision-making, cost optimization, and resource allocation.

---

## Table of Contents
1. [Tools and Techniques](#tools-and-techniques)  
2. [Queries and Analysis](#queries-and-analysis)  
   - Q1 – Q15 (each question includes the query, the result interpretation from the charts, and chart reference)  
3. [Recommendations](#recommendations)  
4. [Limitations](#limitations)  

---

## Tools and Techniques
- **SQL Server 2022 (SSMS)** — data storage, cleaning, and querying  
- **T-SQL** — aggregates, grouping, date diff, rounding, CASE expressions  
- **Excel** — visualizations
- **GitHub** — documentation

---

## Queries and Analysis

> **Note:** Each question below contains (1) the SQL query used, (2) the result interpretation, and (3) the chart filename for reference.

---

### Q1. What is the average age of patients by gender?
**Query**
```sql
SELECT Gender,
       ROUND(AVG(CAST(Age AS FLOAT)), 0) AS Avg_Age
FROM Healthcare_Dataset
GROUP BY Gender;
```

### Result: Both Male and Female patients have the same average age: 51 years.
Chart reference: ![Average age by Gender](Average age by Gender)

### Q2. Which blood type is most common among patients?

**Query**

```sql
SELECT Blood_Type,
       COUNT(*) AS Count_Patients
FROM Healthcare_Dataset
GROUP BY Blood_Type
ORDER BY Count_Patients DESC;
```

### Result: The most common blood types — AB- (1,275), AB+ (1,258), B- (1,252), O+ (1,248). Distribution across types is fairly even.
Chart reference:![Most Common Blood Type]([Most-Common-Blood-Type](https://github.com/Isholaesther/HEALTH-CARE-DATASET-ANALYSIS-SQL-EXCEL-/blob/main/Most-Common-Blood-Type.png))

### Q3. How many patients fall into different age groups (0–18, 19–35, 36–60, 61+)?

**Query**

```sql
SELECT
  CASE
    WHEN Age BETWEEN 0 AND 18 THEN '0-18'
    WHEN Age BETWEEN 19 AND 35 THEN '19-35'
    WHEN Age BETWEEN 36 AND 60 THEN '36-60'
    ELSE '61+'
  END AS Age_Group,
  COUNT(*) AS Patient_Count
FROM Healthcare_Dataset
GROUP BY
  CASE
    WHEN Age BETWEEN 0 AND 18 THEN '0-18'
    WHEN Age BETWEEN 19 AND 35 THEN '19-35'
    WHEN Age BETWEEN 36 AND 60 THEN '36-60'
    ELSE '61+'
  END;
```

### Result: 61+ is the largest group, followed by 36–60, then 19–35, with 0–18 the smallest.
Chart reference: ![Patients by Age Group](Patients-by-Age-Group)

### Q4. What is the distribution of patients by admission type (Emergency, Elective, Urgent)?

**Query**

```sql
SELECT Admission_Type,
       COUNT(*) AS Patient_Count
FROM Healthcare_Dataset
GROUP BY Admission_Type;
```

### Result: Urgent: 3,391, Emergency: 3,367, Elective: 3,242. Urgent and Emergency dominate admissions.
Chart reference: ![Distribution by Admission Type](Distribution-by-Admission-Type)

### Q5. Which hospital has admitted the highest number of patients?

**Query**

```sql
SELECT TOP 1 Hospital,
       COUNT(*) AS Patient_Count
FROM Healthcare_Dataset
GROUP BY Hospital
ORDER BY Patient_Count DESC;
```

### Result: Smith PLC admitted the highest number of patients (19 admissions). Next highest: Smith and Sons (17), Smith Inc (14), Smith Ltd (14), Johnson PLC (13).
Chart reference: ![Hospital With Highest Admission](Hospital-With-Highest-Admission)

Q6. What is the average length of stay (Discharge – Admission) by hospital?

**Query (Top 3)**

```sql
SELECT TOP 3 Hospital,
       AVG(DATEDIFF(DAY, Date_of_Admission, Discharge_Date)) AS Avg_Length_Stay
FROM Healthcare_Dataset
GROUP BY Hospital
ORDER BY Avg_Length_Stay DESC;
```

**Query (Bottom 3)**

```sql
SELECT TOP 3 Hospital,
       AVG(DATEDIFF(DAY, Date_of_Admission, Discharge_Date)) AS Avg_Length_Stay
FROM Healthcare_Dataset
GROUP BY Hospital
ORDER BY Avg_Length_Stay ASC;
```

### Result:

Top 3 (longest avg stay) — Carter-Riley (30 days), Young, Ashley (30 days), Parsons, Olson (30 days).

Bottom 3 (shortest avg stay) — Lam, Rodriguez and Berger (1 day), Horn Group (1 day), Howard Ltd (1 day).
Chart reference:

### Q7. Which medications are prescribed most frequently for each medical condition?

**Query**

```sql
SELECT Medical_Condition,
       Medication,
       COUNT(*) AS Times_Prescribed
FROM Healthcare_Dataset
GROUP BY Medical_Condition, Medication
ORDER BY Medical_Condition, Times_Prescribed DESC;
```

### Result:

Obesity → Paracetamol (332)

Hypertension → Lipitor (356)

Diabetes → Aspirin (335)

Cancer → Penicillin (364)

Asthma → Aspirin (370)

Arthritis → Penicillin (366)
Chart reference:

### Q8. Which doctors have treated the most patients for each condition?

**Query**

```sql

SELECT Medical_Condition,
       Doctor,
       COUNT(*) AS Patient_Count
FROM Healthcare_Dataset
GROUP BY Medical_Condition, Doctor
ORDER BY Medical_Condition, Patient_Count DESC;
```

### Result: Leading doctors per condition:

Obesity → Jennifer Smith

Hypertension → Jennifer Smith

Diabetes → Matthew Gonzalez

Cancer → Zachary Smith

Asthma → Michael Smith

Arthritis → Patricia Moore
Chart reference:

### Q9. Which insurance provider covers the highest total billing amount?

**Query**

```sql
SELECT TOP 1 Insurance_Provider,
       ROUND(SUM(Billing_Amount), 0) AS Total_Billing
FROM Healthcare_Dataset
GROUP BY Insurance_Provider
ORDER BY Total_Billing DESC;
```

### Result: Cigna has the highest total billing (~$52,340,172). Next: Aetna (~$52,321,795), Blue Cross (~$52,125,859).
Chart reference:

### Q10. What is the average billing amount per admission type?

**Query**
```sql

SELECT Admission_Type,
       ROUND(AVG(Billing_Amount), 0) AS Avg_Billing
FROM Healthcare_Dataset
GROUP BY Admission_Type;
```

### Result: Urgent: $25,961, Elective: $25,892, Emergency: $24,709.
Chart reference:

### Q11. Which doctor has the highest average billing per patient?

**Query**

```sql
SELECT TOP 1 Doctor,
       ROUND(AVG(Billing_Amount), 0) AS Avg_Billing
FROM Healthcare_Dataset
GROUP BY Doctor
ORDER BY Avg_Billing DESC;
```

### Result: Aaron Mills has the highest average billing — approximately $50,000 per patient.
Chart reference:

### Q12. Which doctor treated the most patients in emergency admissions?

**Query**
```sql
SELECT TOP 2 Doctor,
       COUNT(*) AS Emergency_Patients
FROM Healthcare_Dataset
WHERE Admission_Type = 'Emergency'
GROUP BY Doctor
ORDER BY Emergency_Patients DESC;
```

### Result: Jennifer Smith treated the most emergency patients (4), followed by Christopher Jones (3).
Chart reference:

### Q13. What percentage of patients had “Normal”, “Abnormal”, or “Inconclusive” test results?

**Query**
```sql

SELECT Test_Results,
       CAST(ROUND(COUNT(*) * 100.0 / (SELECT COUNT(*) FROM Healthcare_Dataset), 1) AS DECIMAL(5,1)) AS Percentage
FROM Healthcare_Dataset
GROUP BY Test_Results;
```

### Result: Normal: 34.6%, Abnormal: 32.8%, Inconclusive: 32.7%.
Chart reference:

### Q14. Is there a relationship between medical condition and test results?

**Query**
```sql
SELECT Medical_Condition,
       Test_Results,
       COUNT(*) AS Result_Count
FROM Healthcare_Dataset
GROUP BY Medical_Condition, Test_Results
ORDER BY Medical_Condition, Result_Count DESC;
```

### Result: The chart shows relatively balanced distributions of Normal / Abnormal / Inconclusive across conditions with minor differences 
Chart reference: 

### Q15. Which hospital records the highest percentage of abnormal test results?

**Query**

```sql
SELECT Hospital,
       CAST(ROUND(SUM(CASE WHEN Test_Results = 'Abnormal' THEN 1 ELSE 0 END) * 100.0 / COUNT(*), 1) AS DECIMAL(5,1)) AS Abnormal_Percentage
FROM Healthcare_Dataset
GROUP BY Hospital
ORDER BY Abnormal_Percentage DESC;
```

### Result: Smith and Sons had the highest abnormal count (7).
Chart reference:

## Recommendations

1. Review hospitals with high average stays (top 3) for operational or clinical causes; check for delayed discharges or complex case mixes.

2. Investigate hospitals with higher abnormal test counts/rates to determine whether extra diagnostic resources or case reviews are needed.

3. Audit outlier billing patterns (doctors with very high average billing) for coding or cost-efficiency checks.

4. Resource planning: urgent & emergency volumes suggest focusing staffing on peak times/units.

5. Preventive care focus for conditions linked to higher abnormal results (target screening and follow-ups).

## Limitations

1. The dataset used is limited to the provided records and may not reflect broader populations or seasonal trends.

2. Any rounding shown in charts is for readability; queries provided return exact counts if you remove rounding.
