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

### Result: 
The average age is consistent across both male and female patients at approximately 51 years, suggesting a balanced age distribution between genders. This indicates that age-related healthcare demand in this dataset does not significantly differ by gender.

Chart reference: ![Average_age_by_Gender](Average age by Gender)

### Q2. Which blood type is most common among patients?

**Query**

```sql
SELECT Blood_Type,
       COUNT(*) AS Count_Patients
FROM Healthcare_Dataset
GROUP BY Blood_Type
ORDER BY Count_Patients DESC;
```

### Result: 
Blood types are relatively evenly distributed across the patient population, with AB-, AB+, B-, and O+ appearing slightly more frequently. The absence of a dominant blood type suggests no strong skew that would impact resource planning based on blood group demand.
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

### Result: 
Patients aged 61+ represent the largest segment, followed by those aged 36–60, indicating a higher concentration of older individuals within the dataset. This may imply increased healthcare utilization among aging populations, which is consistent with typical healthcare demand patterns.
Chart reference: ![Patients by Age Group](Patients-by-Age-Group)

### Q4. What is the distribution of patients by admission type (Emergency, Elective, Urgent)?

**Query**

```sql
SELECT Admission_Type,
       COUNT(*) AS Patient_Count
FROM Healthcare_Dataset
GROUP BY Admission_Type;
```

### Result: 
Urgent and emergency admissions slightly exceed elective cases, indicating a higher proportion of unplanned or critical care needs. This suggests that healthcare facilities may need to prioritize readiness for acute cases over scheduled procedures.
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

### Result: 
Patient admissions are relatively low and evenly distributed across hospitals, with the highest recorded count being 19. This suggests either a limited dataset or a highly fragmented hospital representation, which may constrain meaningful comparison at the facility level.
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

There is significant variability in average length of stay across hospitals, ranging from 1 to 30 days. Facilities with longer stays may be handling more complex cases or experiencing discharge inefficiencies, while shorter stays could indicate either operational efficiency or less severe case types.

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

Certain medications are more frequently associated with specific conditions, indicating consistent treatment patterns. However, the recurrence of general medications (e.g., pain relievers) across multiple conditions may suggest standardized symptom management rather than highly specialized treatment approaches.

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

### Result: 
Cigna has the highest total billing (~$52,340,172). Next: Aetna (~$52,321,795), Blue Cross (~$52,125,859).
Chart reference:

### Q10. What is the average billing amount per admission type?

**Query**
```sql

SELECT Admission_Type,
       ROUND(AVG(Billing_Amount), 0) AS Avg_Billing
FROM Healthcare_Dataset
GROUP BY Admission_Type;
```

### Result: 
Urgent: $25,961, Elective: $25,892, Emergency: $24,709. Average billing amounts are fairly consistent across admission types, with urgent admissions slightly higher. This indicates that cost variations are not strongly driven by admission category alone and may depend more on treatment complexity or duration of care.
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

### Result: Aaron Mills has the highest average billing — approximately $50,000 per patient. This may indicate involvement in more complex or high-cost cases. However, this could also warrant further review to rule out anomalies in billing practices or data distribution.
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

### Result: Normal: 34.6%, Abnormal: 32.8%, Inconclusive: 32.7%. Test outcomes are nearly evenly split between Normal, Abnormal, and Inconclusive results. This balanced distribution suggests no dominant diagnostic outcome trend within the dataset.
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

### Result: Test result distributions remain relatively consistent across different medical conditions, with no strong correlation observed. This may indicate that outcomes are influenced by factors beyond the condition itself, such as treatment timing or patient-specific variables.
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

### Result: Smith and Sons had the highest abnormal count (7). However, differences in abnormal test result rates across hospitals are minimal, with the highest counts remaining relatively low. This limits the ability to draw strong conclusions about hospital-level performance based on this metric alone.
Chart reference:

## Recommendations

1. The dataset appears to have relatively small and evenly distributed counts across key dimensions (e.g., hospitals, doctors), which may limit the statistical significance of comparisons and reduce the reliability of identifying true outliers.
2. The absence of additional contextual variables—such as severity of illness, treatment type, or patient history—restricts deeper analysis and prevents more robust conclusions about cost drivers and clinical outcomes.
3. Some metrics (e.g., average billing or length of stay) may be influenced by outliers, and without distribution analysis (e.g., median, variance), results should be interpreted with caution.
4. The dataset structure suggests potential fragmentation (e.g., low counts per hospital), which may not accurately reflect real-world healthcare systems and could impact the generalizability of insights.
5. Rounding applied for reporting purposes may slightly obscure precise differences between categories, although it improves readability.

## Limitations

1. The dataset used is limited to the provided records and may not reflect broader populations or seasonal trends.

2. Any rounding shown in charts is for readability; queries provided return exact counts if you remove rounding.
