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


### Total Number of Orders
```sql
SELECT SUM(Amount)
FROM [Business Intel_Phone Sales_Prep with Calendar Table2];
```
### Output
||Total_Orders|
 -----|----------|
1 | 152344|


#### The total number of orders is 152,344 within the four years (2018-2021)

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


### Brand with highest number of orders
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

#### LG and Samsung are brand which produces both luxury and affordable product which explains why they have the highest order


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

#### - In 2018, Guatemala and Oeschle(Distibutor) has the best sales performance of a total sales of $868,460 
#### -In 2019, Columbia  and Tottus (a distributor) has the best sales performance of a total of $1,305,000
#### -In 2020, USA and Tottus (a distributor) has the best sales performance of a total of $899,300
#### -In 2021, Columbia and Oeschle(Distibutor) has the best sales performance of a total of $899,300

### Each year, which brand is the best seller in each country and distributor?
```sql
SELECT Brand, Country, Distributor, YEAR_N
FROM (
SELECT YEAR_N, Brand, Country, Distributor, SUM(Sales) AS Total_sales, 
       ROW_NUMBER() OVER (PARTITION BY YEAR_N ORDER BY SUM(Sales)DESC) AS RANK_S
	   FROM [Business Intel_Phone Sales_Prep with Calendar Table2]
	    GROUP BY Brand, Country, Distributor, YEAR_N) AS RANK_S
		WHERE RANK_S = 1;
```
### Output

Brand| Country | Distributor | YEAR_N
--| -- | -- |--
Apple|	Cuba |	Oeschle	| 2018
Apple |	Colombia |	Tottus | 2019
Samsung	| Portugal | Tottus	| 2020
LG	| Colombia	| Tottus | 2021

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
   Country | Distributor
   -- | --
   Argentina | Tottus
Bolivia	| Tottus
Brasil	| Tottus
Canada	| Tottus
Chile	| Oeschle
Colombia | Tottus
Cuba | Oeschle
Denmark	| Tottus
El Salvador	| Tottus
France	| Oeschle
Germany	| Oeschle
Guatemala	| Tottus
Ireland	| Tottus
Jamaica	| Oeschle
Mexico	| Oeschle
Paraguay | Plaza Vea
Peru | Oeschle
Portugal | Tottus
Spain | Tottus
Switzerland | Plaza Vea
United Kingdom	| Tottus
Uruguay	| Oeschle
USA	| Tottus
Venezuela	| Tottus

### Which country had the highest inventory cost from 2018-2021?
```sql
SELECT Country, YEAR_N 
FROM (
 SELECT Country, YEAR_N, SUM(Unit_Cost) AS Inventory_cost,
 ROW_NUMBER() OVER (PARTITION BY YEAR_N ORDER BY SUM(Unit_Cost) DESC) AS RANK_S
 FROM [Business Intel_Phone Sales_Prep with Calendar Table2]
 GROUP BY Country,YEAR_N) AS RANK_S
 WHERE RANK_S = 1;
 ```

 ### Output
 Country | YEAR_N
 -- | --
 Cuba	| 2018
Denmark	| 2019
Chile	| 2020
Ireland |	2021

#### Which country had the lowest inventory cost from 2018-2021?
```sql
SELECT Country, YEAR_N 
FROM (
 SELECT Country, YEAR_N, SUM(Unit_Cost) AS Inventory_cost,
 ROW_NUMBER() OVER (PARTITION BY YEAR_N ORDER BY SUM(Unit_Cost) ASC) AS RANK_S
 FROM [Business Intel_Phone Sales_Prep with Calendar Table2]
 GROUP BY Country,YEAR_N) AS RANK_S
 WHERE RANK_S = 1;
 ```
Country | YEAR_N
-- | --
 Mexico	| 2018
Venezuela	| 2019
Switzerland	| 2020
Frace |	2021