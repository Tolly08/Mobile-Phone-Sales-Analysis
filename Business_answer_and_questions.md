## Business Questions

This is an Exploratory Data Analysis (EDA) and the goal is to explore the business patterns in the data and summarize discoveries into meaningful sales insights.

---

### Calculating the KPI 
### Total Sales
```sql
SELECT SUM(Sales) AS Total_Sales
FROM [Business Intel_Phone Sales_Prep with Calendar Table2];
```

### Output:
 ||Total_Sales|
 -----|----------|
1 | 12720480000

--Total Number of Orders
```sql
SELECT SUM(Amount)
FROM [Business Intel_Phone Sales_Prep with Calendar Table2];
```
### Output
||Total_Orders|
 -----|----------|
1 | 152344|
