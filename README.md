# Carbon-Emission-Analysis

## 1. Introduction

This report aims to analyze carbon emissions to examine the carbon footprint across various industries. We aim to identify sectors with the highest levels of emissions by analyzing them across countries and years, as well as to uncover trends.

Carbon emissions play a crucial role in the environment, accounting for over 75% of global emissions and posing a significant environmental challenge. These emissions contribute to the accumulation of greenhouse gases in the atmosphere, leading to climate change, planetary warming, and involvement in various environmental disasters.

Through this analysis, we hope to gain an understanding of the environmental impact of different industries and contribute to making informed decisions in sustainable development.

## 2. Dataset
### 2.1. Data source

Our dataset is compiled from publicly available data from nature.com and encompasses the product carbon footprints (PCF) for various companies. PCFs represent the greenhouse gas emissions associated with specific products, quantified in CO2 (carbon dioxide equivalent).

### 2.2. Data model

The dataset consists of 4 tables containing information regarding carbon emissions generated during the production of goods.

#### 2.2.1. Table 'product_emissions'
```
SELECT *
FROM product_emissions
LIMIT 10;
```

| id           | company_id | country_id | industry_group_id | year | product_name                                                    | weight_kg | carbon_footprint_pcf | upstream_percent_total_pcf | operations_percent_total_pcf | downstream_percent_total_pcf | 
| -----------: | ---------: | ---------: | ----------------: | ---: | --------------------------------------------------------------: | --------: | -------------------: | -------------------------: | ---------------------------: | ---------------------------: | 
| 10056-1-2014 | 82         | 28         | 2                 | 2014 | Frosted Flakes(R) Cereal                                        | 0.7485    | 2                    | 57.50                      | 30.00                        | 12.50                        | 
| 10056-1-2015 | 82         | 28         | 15                | 2015 | "Frosted Flakes, 23 oz, produced in Lancaster, PA (one carton)" | 0.7485    | 2                    | 57.50                      | 30.00                        | 12.50                        | 
| 10222-1-2013 | 83         | 28         | 8                 | 2013 | Office Chair                                                    | 20.68     | 73                   | 80.63                      | 17.36                        | 2.01                         | 
| 10261-1-2017 | 14         | 16         | 25                | 2017 | Multifunction Printers                                          | 110       | 1488                 | 30.65                      | 5.51                         | 63.84                        | 
| 10261-2-2017 | 14         | 16         | 25                | 2017 | Multifunction Printers                                          | 110       | 1818                 | 25.08                      | 4.51                         | 70.41                        | 

#### 2.2.2. Table 'companies'

```
SELECT *
FROM companies
LIMIT 5;
```

| id | company_name                  | 
| -: | ----------------------------: | 
| 1  | "Autodesk, Inc."              | 
| 2  | "Casio Computer Co., Ltd."    | 
| 3  | "Cisco Systems, Inc."         | 
| 4  | "CNX Coal Resources, LP"      | 
| 5  | "Coca-Cola Enterprises, Inc." | 

#### 2.2.3. Table 'countries'

```
SELECT *
FROM countries
LIMIT 5;
```

| id | country_name | 
| -: | -----------: | 
| 1  | Australia    | 
| 2  | Belgium      | 
| 3  | Brazil       | 
| 4  | Canada       | 
| 5  | Chile        | 

#### 2.2.4. Table 'industry_groups'

```
SELECT *
FROM industry_groups
LIMIT 5;
```

| id | industry_group                                                         | 
| -: | ---------------------------------------------------------------------: | 
| 1  | "Consumer Durables, Household and Personal Products"                   | 
| 2  | "Food, Beverage & Tobacco"                                             | 
| 3  | "Forest and Paper Products - Forestry, Timber, Pulp and Paper, Rubber" | 
| 4  | "Mining - Iron, Aluminum, Other Metals"                                | 
| 5  | "Pharmaceuticals, Biotechnology & Life Sciences"                       | 

## 3. Data preprocessing

### 3.1. Remove duplicates

### 3.2. Remove N/A values

## 4. Data processing

### 4.1. Which products contribute the most to carbon emissions?

```
SELECT
    product_name
    ROUND(SUM(carbon_footprint_pcf), 2) AS total_emissions
FROM product_emissions
GROUP BY product_name
ORDER BY total_emissions DESC
LIMIT 10;
```

| product_name                                                                                                                       | total_emissions | 
| ---------------------------------------------------------------------------------------------------------------------------------: | --------------: | 
| Wind Turbine G128 5 Megawats                                                                                                       | 3718044.00      | 
| Wind Turbine G132 5 Megawats                                                                                                       | 3276187.00      | 
| Wind Turbine G114 2 Megawats                                                                                                       | 1532608.00      | 
| Wind Turbine G90 2 Megawats                                                                                                        | 1251625.00      | 
| TCDE                                                                                                                               | 198150.00       | 
| Land Cruiser Prado. FJ Cruiser. Dyna trucks. Toyoace.IMV def unit.                                                                 | 191687.00       | 
| Retaining wall structure with a main wall (sheet pile): 136 tonnes of steel sheet piles and 4 tonnes of tierods per 100 meter wall | 167000.00       | 
| Electric Motor                                                                                                                     | 160655.00       | 
| Audi A6                                                                                                                            | 111282.00       | 
| Average of all GM vehicles produced and used in the 10 year life-cycle.                                                            | 100621.00       | 

**Explanation**


This query identifies the top 10 products with the highest total carbon emissions recorded in the dataset. It joins the product_emissions and industry_groups tables to display each product's industry group. The carbon_footprint_pcf values are summed and rounded to two decimal places, and results are sorted in descending order of emissions.

**Insights & Conclusions**
* Wind turbines dominate the emissions chart, with the G128 and G132 models each responsible for over 3 million kg of CO₂ equivalent. While wind energy is clean in use, their production (especially the steel and composite components) carries a heavy carbon footprint.
* The Electrical Equipment and Machinery industry clearly contributes significantly due to large-scale infrastructure products like turbines.
* Vehicles, including Land Cruiser Prado, Audi A6, and GM fleet averages, appear multiple times, reflecting the high impact of automotive production and long-term use.
* Material-intensive infrastructure products like retaining walls and industrial equipment such as electric motors also rank high in emissions.
* These results highlight that industrial and infrastructure products—despite potentially supporting sustainability—can carry significant upfront environmental costs. This underscores the importance of considering life-cycle emissions when evaluating product sustainability.

### 4.2. What are the industry groups of these products?
```
SELECT
    pe.product_name,
    ig.industry_group,
    ROUND(SUM(pe.carbon_footprint_pcf), 2) AS total_emissions
FROM product_emissions pe
JOIN industry_groups ig ON pe.industry_group_id = ig.id
GROUP BY
    pe.product_name,
    ig.industry_group
ORDER BY total_emissions DESC
LIMIT 10;
```

| product_name                                                                                                                       | industry_group                     | total_emissions | 
| ---------------------------------------------------------------------------------------------------------------------------------: | ---------------------------------: | --------------: | 
| Wind Turbine G128 5 Megawats                                                                                                       | Electrical Equipment and Machinery | 3718044.00      | 
| Wind Turbine G132 5 Megawats                                                                                                       | Electrical Equipment and Machinery | 3276187.00      | 
| Wind Turbine G114 2 Megawats                                                                                                       | Electrical Equipment and Machinery | 1532608.00      | 
| Wind Turbine G90 2 Megawats                                                                                                        | Electrical Equipment and Machinery | 1251625.00      | 
| TCDE                                                                                                                               | Materials                          | 198150.00       | 
| Land Cruiser Prado. FJ Cruiser. Dyna trucks. Toyoace.IMV def unit.                                                                 | Automobiles & Components           | 191687.00       | 
| Retaining wall structure with a main wall (sheet pile): 136 tonnes of steel sheet piles and 4 tonnes of tierods per 100 meter wall | Materials                          | 167000.00       | 
| Electric Motor                                                                                                                     | Capital Goods                      | 140647.00       | 
| Audi A6                                                                                                                            | Automobiles & Components           | 111282.00       | 
| Average of all GM vehicles produced and used in the 10 year life-cycle.                                                            | Automobiles & Components           | 100621.00       | 
