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

### The total sales for the four years (2018-2021) is $127,204,480,000
---

### Total Number of Orders
```sql
SELECT SUM(Amount)
FROM [Business Intel_Phone Sales_Prep with Calendar Table2];
```
### Output
||Total_Orders|
 -----|----------|
1 | 152344|

#### .The total number of orders is 152,344 within the four years (2018-2021)
---
 
 ### Brand with the highest price 
 ```sql
SELECT TOP 1 Brand, MAX(Unit_Price) AS Highest_price
FROM [Business Intel_Phone Sales_Prep with Calendar Table2]
Group BY Brand
ORDER BY Highest_price DESC;
```
### Output
Brand | Highest_Price 
 --|--|
Apple| 1200

#### Apple as a brand are know for luxury which explains why it has the highest price ($1200). Apple excels in providing a seamless, high-quality user experience  
---

### Brand with the Lowest price 
 ```sql
SELECT TOP 1 Brand, MIN(Unit_Price) AS Lowest_price
FROM [Business Intel_Phone Sales_Prep with Calendar Table2]
Group BY Brand
ORDER BY Lowest_price DESC;
```
### Output
Brand | Lowest_price 
 --|--|
Huawei| 450

 #### Huawei offers a wide range of innovative products with a focus on emerging markets thereby making their products affordable
---

###  Brand with highest number of orders
``` sql
SELECT Brand, SUM(DISTINCT AMOUNT) AS Total_Orders
 FROM [Business Intel_Phone Sales_Prep with Calendar Table2]
 GROUP BY Brand
 ORDER BY Total_Orders DESC;
 ```
### Output
Brand | Total_Orders
-- | --
LG	| 11197
Samsung | 9341
Apple	| 8863
Nokia	| 7294
Motorola	| 6679
Huawei	| 5827

#### LG and Samsung are brand which produces both luxury and affordable product(phones), Samsung has which explains why they have the highest order
---

### Extracting year from Date and update the table 
```sql
ALTER TABLE [Business Intel_Phone Sales_Prep with Calendar Table2]
ADD YEAR_N INT;
```
```sql
UPDATE [Business Intel_Phone Sales_Prep with Calendar Table2]
SET YEAR_N = YEAR(DATE);
```

### Extracting month from Date and update the table
```sql
ALTER TABLE [Business Intel_Phone Sales_Prep with Calendar Table2]
ADD MONTH_NEW NVARCHAR (50);
```

```sql
UPDATE [Business Intel_Phone Sales_Prep with Calendar Table2]
SET MONTH_NEW = DATENAME(Month,[Date]);
``` 
---

### This leads us to the next question

### From 2018-2021, which country and distributor had the best sales performance?

```sql
SELECT Distributor, Country, Total_sales, YEAR_N
FROM (
SELECT YEAR_N,Country, Distributor,SUM(Sales) AS Total_sales,
     ROW_NUMBER() OVER (PARTITION BY YEAR_N ORDER BY SUM(Sales) DESC) AS RANK_S
	 FROM [Business Intel_Phone Sales_Prep with Calendar Table2]
GROUP BY Distributor, Country, YEAR_N) AS Ranked_sales
WHERE RANK_S =1;
```

### Output 

Distributor| Country | Total_sales | YEAR_N
-- |-- |-- | --
Oeschle	| Guatemala	| 868460 | 2018
Tottus	| Colombia	| 1305000 |	2019
Tottus	| USA |	899300 | 2020
Oeschle	| Colombia |	1464400	|2021

## Insights 

#### - In 2018, Guatemala and Oeschle(Distibutor) has the best sales performance of a total sales of $868,460 
#### -In 2019, Columbia  and Tottus (a distributor) has the best sales performance of a total of $1,305,000
#### -In 2020, USA and Tottus (a distributor) has the best sales performance of a total of $899,300
#### -In 2021, Columbia and Oeschle(Distibutor) has the best sales performance of a total of $1,464,400
---

### Each year, which brand is the best seller in each country and distributor?
```sql
SELECT Brand, Country, Distributor, YEAR_N, Total_sales
FROM (
SELECT YEAR_N, Brand, Country, Distributor, SUM(Sales) AS Total_sales, 
       ROW_NUMBER() OVER (PARTITION BY YEAR_N ORDER BY SUM(Sales)DESC) AS RANK_S
	   FROM [Business Intel_Phone Sales_Prep with Calendar Table2]
	    GROUP BY Brand, Country, Distributor, YEAR_N) AS RANK_S
		WHERE RANK_S = 1;
```
### Output

Brand| Country | Distributor | YEAR_N | Total_sales
--| -- | -- |-- | --
Apple|	Cuba |	Oeschle	| 2018 | 691200
Apple |	Colombia |	Tottus | 2019 | 1188000
Samsung	| Portugal | Tottus	| 2020 | 544480
LG	| Colombia	| Tottus | 2021 | 793600

### Insight
#### - Apple has the best sales in 2018 and 2019 with sales of $691,200 and 1,118,000 respectively which reduced in 2020 which might be due to the pandemic.

#### -In 2020, Samsung was the best selling brand with total sales of $793,600 Samsung offers a wide range of smartphones that cater to various market segments. From budget-friendly models to high-end flagship devices, Samsung ensures that there is a product for every consumer, regardless of their budget or needs. This extensive portfolio allows Samsung to capture a broad customer base globally. Which makes them affordable even during pandemic
---

### Who is each country's most successful sales rep and distributor

```sql
SELECT Country, Distributor 
FROM (
SELECT Country, Distributor, SUM(Sales) AS Total_sales,
   ROW_NUMBER() OVER (PARTITION BY Country ORDER BY SUM(Sales)DESC) AS RANK_S
   FROM [Business Intel_Phone Sales_Prep with Calendar Table2] 
   GROUP BY Country, Distributor) AS RANKS_S
   WHERE RANK_S = 1;
   ```
   ### Output
   Country | Distributor | Total_sales
   -- | -- | --
Argentina |	Tottus | 1481420
Bolivia | Tottus | 1826340
Brasil | Tottus |	1412320
Canada | Tottus |	1735020
Chile	| Oeschle |	1712020
Colombia	| Tottus |	3183400
Cuba | Oeschle |	1828020
Denmark | Tottus | 2778740
El Salvador	| Tottus	| 1886240
France | Oeschle	| 2042360
Germany	| Oeschle |	1427960
Guatemala | Tottus |	2025100
Ireland | Tottus	| 1909660
Jamaica | Oeschle |	1468440
Mexico | Oeschle | 1779240
Paraguay	| Plaza Vea	| 1767880
Peru	| Oeschle | 1616480
Portugal	| Tottus	| 1445980
Spain	| Tottus	| 1424960
Switzerland	| Plaza Vea	| 1172320
United Kingdom	| Tottus	| 1800100
Uruguay | Oeschle	| 1493280
USA | Tottus | 1470480
Venezuela | Tottus |	1226300

### Which country had the highest inventory cost from 2018-2021?
```sql
SELECT Country, YEAR_N, Inventory_cost
FROM (
 SELECT Country, YEAR_N, SUM(Unit_Cost) AS Inventory_cost,
 ROW_NUMBER() OVER (PARTITION BY YEAR_N ORDER BY SUM(Unit_Cost) DESC) AS RANK_S
 FROM [Business Intel_Phone Sales_Prep with Calendar Table2]
 GROUP BY Country,YEAR_N) AS RANK_S
 WHERE RANK_S = 1;
 ```

 ### Output
 Country | YEAR_N | Inventory_cost
  -- | -- | --
 Cuba	| 2018 | 23900
Denmark	| 2019 | 23270
Chile	| 2020 | 23690
Ireland | 2021 | 35790
---
#### Which country had the lowest inventory cost from 2018-2021?
```sql
SELECT Country, YEAR_N, Inventory_cost
FROM (
 SELECT Country, YEAR_N, SUM(Unit_Cost) AS Inventory_cost,
 ROW_NUMBER() OVER (PARTITION BY YEAR_N ORDER BY SUM(Unit_Cost) ASC) AS RANK_S
 FROM [Business Intel_Phone Sales_Prep with Calendar Table2]
 GROUP BY Country,YEAR_N) AS RANK_S
 WHERE RANK_S = 1;
 ```
Country | YEAR_N | Inventory_cost
-- | -- | --
 Mexico	| 2018 | 7590
Venezuela	| 2019 | 5670
Switzerland	| 2020 | 4640
France |	2021 | 18150