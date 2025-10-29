# carbon-emission-analysis
This report aims to analyze carbon emissions to examine the carbon footprint across various industries. We aim to identify sectors with the highest levels of emissions by analyzing them across countries and years, as well as to uncover trends.
## Overview

<img width="640" height="427" alt="image" src="https://github.com/user-attachments/assets/df926a86-d20f-46c4-90fa-eaca7bb105ff" />\

<b>What is the project about?</b>\
Carbon emissions play a crucial role in the environment, accounting for over 75% of global emissions and posing a significant environmental challenge. These emissions contribute to the accumulation of greenhouse gases in the atmosphere, leading to climate change, planetary warming, and involvement in various environmental disasters.

Through this analysis, we hope to gain an understanding of the environmental impact of different industries and contribute to making informed decisions in sustainable development.

## Data Structure
### Table 'product_emissions'
Query
```sql
SELECT * FROM product_emissions pe 
LIMIT 5;
```
Result:
|id|company_id|country_id|industry_group_id|year|product_name|weight_kg|carbon_footprint_pcf|upstream_percent_total_pcf|operations_percent_total_pcf|downstream_percent_total_pcf|
|--|----------|----------|-----------------|----|------------|---------|--------------------|--------------------------|----------------------------|----------------------------|
|10056-1-2014|82|28|2|2014|Frosted Flakes(R) Cereal|0.7485|2|57.50|30.00|12.50|
|10056-1-2015|82|28|15|2015|"Frosted Flakes, 23 oz, produced in Lancaster, PA (one carton)"|0.7485|2|57.50|30.00|12.50|
|10222-1-2013|83|28|8|2013|Office Chair|20.68|73|80.63|17.36|2.01|
|10261-1-2017|14|16|25|2017|Multifunction Printers|110.0|1488|30.65|5.51|63.84|
|10261-2-2017|14|16|25|2017|Multifunction Printers|110.0|1818|25.08|4.51|70.41|

### Table 'company'
Query
```sql
SELECT * FROM companies comp 
LIMIT 5;
```
Result:
|id|company_name|
|--|------------|
|1|"Autodesk, Inc."|
|2|"Casio Computer Co., Ltd."|
|3|"Cisco Systems, Inc."|
|4|"CNX Coal Resources, LP"|
|5|"Coca-Cola Enterprises, Inc."|

### Table 'countries'
Query
```sql
SELECT * FROM countries ct
LIMIT 5;
```
Result:
|id|country_name|
|--|------------|
|1|Australia|
|2|Belgium|
|3|Brazil|
|4|Canada|
|5|Chile|

### Table 'industry_groups'
Query
```sql
SELECT * FROM  industry_groups ig 
LIMIT 5;
```
Result:
|id|industry_group|
|--|--------------|
|1|"Consumer Durables, Household and Personal Products"|
|2|"Food, Beverage & Tobacco"|
|3|"Forest and Paper Products - Forestry, Timber, Pulp and Paper, Rubber"|
|4|"Mining - Iron, Aluminum, Other Metals"|
|5|"Pharmaceuticals, Biotechnology & Life Sciences"|

## Detail Analysis
## Check duplicate for Products
Query
```sql
SELECT COUNT(product_name) AS 'Total number of product', 
		COUNT(DISTINCT(product_name)) AS 'Number of distinct products'
FROM product_emissions pe 
```
Result:
|Total number of product|Number of distinct products|
|-----------------------|---------------------------|
|1037|661|
There are 376 duplicate rows for product, it's needed to group by product when calculating carbon emissions (PCF).

## 1.Which products contribute the most to carbon emissions?
Query
```sql
SELECT	
	pe.product_name,
	ROUND(AVG(pe.carbon_footprint_pcf ),2) AS Average_cacbon_footprint
FROM product_emissions pe
GROUP BY pe.product_name 
ORDER BY Average_cacbon_footprint DESC
LIMIT 10;
```
Result:
|product_name|Average_cacbon_footprint|
|------------|------------------------|
|Wind Turbine G128 5 Megawats|3718044.00|
|Wind Turbine G132 5 Megawats|3276187.00|
|Wind Turbine G114 2 Megawats|1532608.00|
|Wind Turbine G90 2 Megawats|1251625.00|
|Land Cruiser Prado. FJ Cruiser. Dyna trucks. Toyoace.IMV def unit.|191687.00|
|Retaining wall structure with a main wall (sheet pile): 136 tonnes of steel sheet piles and 4 tonnes of tierods per 100 meter wall|167000.00|
|TCDE|99075.00|
|Mercedes-Benz GLE (GLE 500 4MATIC)|91000.00|
|Mercedes-Benz S-Class (S 500)|85000.00|
|Mercedes-Benz SL (SL 350)|72000.00|

The most products contribute to carbon emissions is Wind Turbine following is car.

## 2.What are the industry groups of these products?
Query
```sql
SELECT	
	pe.product_name,
	industry_group,
	ROUND(AVG(pe.carbon_footprint_pcf ),2) AS Average_cacbon_footprint
FROM product_emissions pe
JOIN industry_groups ig 
	ON pe.industry_group_id = ig.id
GROUP BY pe.product_name 
ORDER BY Average_cacbon_footprint DESC
LIMIT 10;
```
Result:
|product_name|industry_group|Average_cacbon_footprint|
|------------|--------------|------------------------|
|Wind Turbine G128 5 Megawats|Electrical Equipment and Machinery|3718044.00|
|Wind Turbine G132 5 Megawats|Electrical Equipment and Machinery|3276187.00|
|Wind Turbine G114 2 Megawats|Electrical Equipment and Machinery|1532608.00|
|Wind Turbine G90 2 Megawats|Electrical Equipment and Machinery|1251625.00|
|Land Cruiser Prado. FJ Cruiser. Dyna trucks. Toyoace.IMV def unit.|Automobiles & Components|191687.00|
|Retaining wall structure with a main wall (sheet pile): 136 tonnes of steel sheet piles and 4 tonnes of tierods per 100 meter wall|Materials|167000.00|
|TCDE|Materials|99075.00|
|Mercedes-Benz GLE (GLE 500 4MATIC)|Automobiles & Components|91000.00|
|Mercedes-Benz S-Class (S 500)|Automobiles & Components|85000.00|
|Mercedes-Benz SL (SL 350)|Automobiles & Components|72000.00|

Electrical Equipment and Machinery is the industry group that produce the Wind Turbine. Other products produce by related company.

## 3.What are the industries with the highest contribution to carbon emissions?
Query
```sql
SELECT	
	industry_group,
	ROUND(SUM(pe.carbon_footprint_pcf ),2) AS SUM_cacbon_footprint
FROM product_emissions pe
JOIN industry_groups ig 
	ON pe.industry_group_id = ig.id
GROUP BY industry_group 
ORDER BY SUM_cacbon_footprint DESC
LIMIT 10;
```
Result:
|industry_group|SUM_cacbon_footprint|
|--------------|--------------------|
|Electrical Equipment and Machinery|9801558.00|
|Automobiles & Components|2582264.00|
|Materials|577595.00|
|Technology Hardware & Equipment|363776.00|
|Capital Goods|258712.00|
|"Food, Beverage & Tobacco"|111131.00|
|"Pharmaceuticals, Biotechnology & Life Sciences"|72486.00|
|Chemicals|62369.00|
|Software & Services|46544.00|
|Media|23017.00|

The highest industry contribution to carbon emissions is Electrical Equipment and Machinery industry following are Automobiles & Components industry and Materials industry.

## 4.What are the companies with the highest contribution to carbon emissions?
Query
```sql
SELECT	
	company_name,
	ROUND(SUM(pe.carbon_footprint_pcf ),2) AS SUM_cacbon_footprint
FROM product_emissions pe
JOIN companies comp 
	ON pe.industry_group_id = comp.id
GROUP BY  company_name
ORDER BY SUM_cacbon_footprint DESC
LIMIT 10;
```
Result:
|company_name|SUM_cacbon_footprint|
|------------|--------------------|
|"Interface, Inc."|9801558.00|
|"Daikin Industries, Ltd."|2582264.00|
|"Osaka Gas Co., Ltd."|577595.00|
|"Toppan Printing Co., Ltd."|363776.00|
|"Elitegroup computer systems co., Ltd."|258712.00|
|"Casio Computer Co., Ltd."|111131.00|
|"Coca-Cola Enterprises, Inc."|72486.00|
|"Fuji Xerox Co., Ltd."|62369.00|
|"Staples, Inc."|46544.00|
|"PepsiCo, Inc."|23017.00|

The highest companies with the highest contribution to carbon emissions is Interface, Inc. company following are Daikin Industries, Ltd. company and Osaka Gas Co., Ltd. company

## 5.What are the countries with the highest contribution to carbon emissions?
Query
```sql
SELECT	
	country_name,
	ROUND(SUM(pe.carbon_footprint_pcf ),2) AS SUM_cacbon_footprint
FROM product_emissions pe
JOIN countries ct
	ON pe.industry_group_id = ct.id
GROUP BY  country_name
ORDER BY SUM_cacbon_footprint DESC
LIMIT 10;
```
Result:
|country_name|SUM_cacbon_footprint|
|------------|--------------------|
|Indonesia|9801558.00|
|Colombia|2582264.00|
|Malaysia|577595.00|
|Switzerland|363776.00|
|Finland|258712.00|
|Belgium|111131.00|
|Chile|72486.00|
|France|62369.00|
|Sweden|46544.00|
|Netherlands|23017.00|

The highest country contribution to carbon emissions is Indonesia following are Colombia and Malaysia.

## 6.What is the trend of carbon footprints (PCFs) over the years?
Query
```sql
SELECT 
	pe.`year`,
	ROUND(SUM(pe.carbon_footprint_pcf ),2) AS SUM_cacbon_footprint,
	ROUND(AVG(pe.carbon_footprint_pcf ),2) AS AVG_cacbon_footprint
FROM product_emissions pe 
GROUP BY pe.`year` 
ORDER BY YEAR
```
Result:
|year|SUM_cacbon_footprint|AVG_cacbon_footprint|
|----|--------------------|--------------------|
|2013|503857.00|2399.32|
|2014|624226.00|2457.58|
|2015|10840415.00|43188.90|
|2016|1640182.00|6891.52|
|2017|340271.00|4050.85|

The highest years produce the carbon footprints is 2015 with total 10840415.00 and average is 43188.90.

## 7.Which industry groups has demonstrated the most notable decrease in carbon footprints (PCFs) over time?
Query
```sql
SELECT 
	pe.`year`,
	ROUND(SUM(pe.carbon_footprint_pcf ),2) AS SUM_cacbon_footprint,
	ROUND(AVG(pe.carbon_footprint_pcf ),2) AS AVG_cacbon_footprint
FROM product_emissions pe 
GROUP BY pe.`year` 
ORDER BY YEAR
```
Result:
|year|SUM_cacbon_footprint|AVG_cacbon_footprint|
|----|--------------------|--------------------|
|2013|503857.00|2399.32|
|2014|624226.00|2457.58|
|2015|10840415.00|43188.90|
|2016|1640182.00|6891.52|
|2017|340271.00|4050.85|

## 7.Which industry groups has demonstrated the most notable decrease in carbon footprints (PCFs) over time?
Query
```sql
SELECT 
	industry_group,
	ROUND(SUM(CASE WHEN pe.`year` = 2013 THEN pe.carbon_footprint_pcf ELSE 0 END ),2) AS '2013',
	ROUND(SUM(CASE WHEN pe.`year` = 2014 THEN pe.carbon_footprint_pcf ELSE 0 END ),2) AS '2014',
	ROUND(SUM(CASE WHEN pe.`year` = 2015 THEN pe.carbon_footprint_pcf ELSE 0 END ),2) AS '2015',
	ROUND(SUM(CASE WHEN pe.`year` = 2016 THEN pe.carbon_footprint_pcf ELSE 0 END ),2) AS '2016',
	ROUND(SUM(CASE WHEN pe.`year` = 2017 THEN pe.carbon_footprint_pcf ELSE 0 END ),2) AS '2017'
FROM product_emissions pe 
JOIN industry_groups ig 
	ON pe.industry_group_id = ig.id
GROUP BY industry_group 
ORDER BY '2017' DESC
```
Result:
|industry_group|2013|2014|2015|2016|2017|
|--------------|----|----|----|----|----|
|"Consumer Durables, Household and Personal Products"|0.00|0.00|931.00|0.00|0.00|
|"Food, Beverage & Tobacco"|4995.00|2685.00|0.00|100289.00|3162.00|
|"Forest and Paper Products - Forestry, Timber, Pulp and Paper, Rubber"|0.00|0.00|8909.00|0.00|0.00|
|"Mining - Iron, Aluminum, Other Metals"|0.00|0.00|8181.00|0.00|0.00|
|"Pharmaceuticals, Biotechnology & Life Sciences"|32271.00|40215.00|0.00|0.00|0.00|
|"Textiles, Apparel, Footwear and Luxury Goods"|0.00|0.00|387.00|0.00|0.00|
|Automobiles & Components|130189.00|230015.00|817227.00|1404833.00|0.00|
|Capital Goods|60190.00|93699.00|3505.00|6369.00|94949.00|
|Chemicals|0.00|0.00|62369.00|0.00|0.00|
|Commercial & Professional Services|1157.00|477.00|0.00|2890.00|741.00|
|Consumer Durables & Apparel|2867.00|3280.00|0.00|1162.00|0.00|
|Containers & Packaging|0.00|0.00|2988.00|0.00|0.00|
|Electrical Equipment and Machinery|0.00|0.00|9801558.00|0.00|0.00|
|Energy|750.00|0.00|0.00|10024.00|0.00|
|Food & Beverage Processing|0.00|0.00|141.00|0.00|0.00|
|Food & Staples Retailing|0.00|773.00|706.00|2.00|0.00|
|Gas Utilities|0.00|0.00|122.00|0.00|0.00|
|Household & Personal Products|0.00|0.00|0.00|0.00|0.00|
|Materials|200513.00|75678.00|0.00|88267.00|213137.00|
|Media|9645.00|9645.00|1919.00|1808.00|0.00|
|Retailing|0.00|19.00|11.00|0.00|0.00|
|Semiconductors & Semiconductor Equipment|0.00|50.00|0.00|4.00|0.00|
|Semiconductors & Semiconductors Equipment|0.00|0.00|3.00|0.00|0.00|
|Software & Services|6.00|146.00|22856.00|22846.00|690.00|
|Technology Hardware & Equipment|61100.00|167361.00|106157.00|1566.00|27592.00|
|Telecommunication Services|52.00|183.00|183.00|0.00|0.00|
|Tires|0.00|0.00|2022.00|0.00|0.00|
|Tobacco|0.00|0.00|1.00|0.00|0.00|
|Trading Companies & Distributors and Commercial Services & Supplies|0.00|0.00|239.00|0.00|0.00|
|Utilities|122.00|0.00|0.00|122.00|0.00|



