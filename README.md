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
SELECT 	*
FROM 	product_emissions
LIMIT 	5;
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
SELECT 	*
FROM 	companies
LIMIT 	5;
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
SELECT 	*
FROM 	countries
LIMIT 	5;
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
SELECT 	*
FROM 	industry_groups
LIMIT 	5;
```

| id | industry_group                                                         | 
| -: | ---------------------------------------------------------------------: | 
| 1  | "Consumer Durables, Household and Personal Products"                   | 
| 2  | "Food, Beverage & Tobacco"                                             | 
| 3  | "Forest and Paper Products - Forestry, Timber, Pulp and Paper, Rubber" | 
| 4  | "Mining - Iron, Aluminum, Other Metals"                                | 
| 5  | "Pharmaceuticals, Biotechnology & Life Sciences"                       | 

## 3. Data processing

### 3.1. Which products contribute the most to carbon emissions?

```
SELECT
	  product_name
    	  ROUND(SUM(carbon_footprint_pcf), 2) AS total_emissions
FROM
	  product_emissions
GROUP BY
	  product_name
ORDER BY
	  total_emissions DESC
LIMIT 	  10;
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

**🧾 Explanation**


This query identifies the top 10 products with the highest total carbon emissions recorded in the dataset. It joins the product_emissions and industry_groups tables to display each product's industry group. The carbon_footprint_pcf values are summed and rounded to two decimal places, and results are sorted in descending order of emissions.

**💡 Insights & Conclusions**
* Wind turbines dominate the emissions chart, with the G128 and G132 models each responsible for over 3 million kg of CO₂ equivalent. While wind energy is clean in use, their production (especially the steel and composite components) carries a heavy carbon footprint.
* The Electrical Equipment and Machinery industry clearly contributes significantly due to large-scale infrastructure products like turbines.
* Vehicles, including Land Cruiser Prado, Audi A6, and GM fleet averages, appear multiple times, reflecting the high impact of automotive production and long-term use.
* Material-intensive infrastructure products like retaining walls and industrial equipment such as electric motors also rank high in emissions.
* These results highlight that industrial and infrastructure products—despite potentially supporting sustainability—can carry significant upfront environmental costs. This underscores the importance of considering life-cycle emissions when evaluating product sustainability.

### 3.2. What are the industry groups of these products?
```
SELECT
    	  pe.product_name,
    	  ig.industry_group,
    	  ROUND(SUM(pe.carbon_footprint_pcf), 2) AS total_emissions
FROM
	  product_emissions pe
JOIN
	  industry_groups ig ON pe.industry_group_id = ig.id
GROUP BY
    	  pe.product_name,
    	  ig.industry_group
ORDER BY
	  total_emissions DESC
LIMIT 	  10;
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

**🧾 Explanation**


This query identifies the top 10 products that contribute the most to carbon emissions in the dataset. It does so by:
* Summing the carbon_footprint_pcf values for each product name.
* Rounding the total emissions to two decimal places.
* Sorting the results in descending order.
* Limiting the output to the top 10 products.
The result combines both product names and their respective industry groups (merged manually or from a join in extended queries) for better contextual understanding.

**💡 Insights & Conclusions**
* Wind turbines, particularly large-scale models like the G128 and G132 (5 Megawatts), dominate the carbon emission chart. Despite being clean energy producers during operation, their manufacturing process is highly carbon-intensive, likely due to heavy materials like steel, copper, and rare earth elements.
* Products from the Electrical Equipment and Machinery industry group take up 4 out of the top 4 spots, confirming the high environmental cost of building large-scale industrial machinery.
* The Automobiles & Components sector also appears multiple times in the top 10, with models such as the Land Cruiser Prado, Audi A6, and even the average GM vehicle contributing significantly to lifecycle emissions.
* Construction-related products like retaining wall structures (Materials) and TCDE also reflect high emissions, pointing to infrastructure as a major contributor.

**🔍 Observation**


Some of the highest-emission products are associated with industries often perceived as environmentally beneficial (e.g., wind turbines), reminding us that sustainable energy still has an environmental cost during manufacturing and setup.

### 3.3. What are the industries with the highest contribution to carbon emissions?

```
SELECT
	ig.industry_group,
	ROUND(SUM(pe.carbon_footprint_pcf), 2) AS total_emissions
FROM
	product_emissions pe
JOIN
	industry_groups ig ON pe.industry_group_id = ig.id
GROUP BY
	ig.industry_group
ORDER BY
	total_emissions DESC;
```

| industry_group                                                         | total_emissions | 
| ---------------------------------------------------------------------: | --------------: | 
| Electrical Equipment and Machinery                                     | 9801558.00      | 
| Automobiles & Components                                               | 2582264.00      | 
| Materials                                                              | 577595.00       | 
| Technology Hardware & Equipment                                        | 363776.00       | 
| Capital Goods                                                          | 258712.00       | 
| "Food, Beverage & Tobacco"                                             | 111131.00       | 
| "Pharmaceuticals, Biotechnology & Life Sciences"                       | 72486.00        | 
| Chemicals                                                              | 62369.00        | 
| Software & Services                                                    | 46544.00        | 
| Media                                                                  | 23017.00        | 
| Energy                                                                 | 10774.00        | 
| "Forest and Paper Products - Forestry, Timber, Pulp and Paper, Rubber" | 8909.00         | 
| "Mining - Iron, Aluminum, Other Metals"                                | 8181.00         | 
| Consumer Durables & Apparel                                            | 7309.00         | 
| Commercial & Professional Services                                     | 5265.00         | 
| Containers & Packaging                                                 | 2988.00         | 
| Tires                                                                  | 2022.00         | 
| Food & Staples Retailing                                               | 1481.00         | 
| "Consumer Durables, Household and Personal Products"                   | 931.00          | 
| Telecommunication Services                                             | 418.00          | 
| "Textiles, Apparel, Footwear and Luxury Goods"                         | 387.00          | 
| Utilities                                                              | 244.00          | 
| Trading Companies & Distributors and Commercial Services & Supplies    | 239.00          | 
| Food & Beverage Processing                                             | 141.00          | 
| Gas Utilities                                                          | 122.00          | 
| Semiconductors & Semiconductor Equipment                               | 54.00           | 
| Retailing                                                              | 30.00           | 
| Semiconductors & Semiconductors Equipment                              | 3.00            | 
| Tobacco                                                                | 1.00            | 
| Household & Personal Products                                          | 0.00            | 

**🧾 Explanation**

This query calculates total carbon emissions per industry by:
* Joining product_emissions with industry_groups to get industry names.
* Summing carbon_footprint_pcf for each industry.
* Rounding totals to 2 decimal places.
* Sorting industries from highest to lowest emissions.
It helps identify which industries contribute most to overall emissions.

**💡 Insights & Conclusions**
* Electrical Equipment and Machinery is by far the largest contributor to carbon emissions, accounting for over 9.8 million CO₂e, significantly ahead of all other sectors.
* The Automobiles & Components and Materials industries are also high emitters, with totals above 2.5 million and 0.5 million CO₂e respectively.
* Notably, even industries like Pharmaceuticals, Biotechnology & Life Sciences, typically perceived as less polluting, show notable emissions — a reminder that every product has a footprint: "Now we know what our health comes at."
* Many industries have negligible emissions (under 1,000 CO₂e), such as Retailing, Tobacco, and Household & Personal Products, which may reflect smaller product footprints or fewer data records.


### 3.4. What are the companies with the highest contribution to carbon emissions?

```
SELECT
	 c.company_name,
	 ROUND(SUM(pe.carbon_footprint_pcf), 2) AS total_emissions
FROM
	 product_emissions pe
JOIN
	 companies c ON pe.company_id = c.id
GROUP BY
	 c.company_name
ORDER BY
	 total_emissions DESC
LIMIT 	 10;
```

| company_name                            | total_emissions | 
| --------------------------------------: | --------------: | 
| "Gamesa Corporación Tecnológica, S.A."  | 9778464.00      | 
| Daimler AG                              | 1594300.00      | 
| Volkswagen AG                           | 655960.00       | 
| "Mitsubishi Gas Chemical Company, Inc." | 212016.00       | 
| "Hino Motors, Ltd."                     | 191687.00       | 
| Arcelor Mittal                          | 167007.00       | 
| Weg S/A                                 | 160655.00       | 
| General Motors Company                  | 137007.00       | 
| "Lexmark International, Inc."           | 132012.00       | 
| "Daikin Industries, Ltd."               | 105600.00       | 

**🧾 Explanation**

This query sums carbon emissions (carbon_footprint_pcf) per company by joining product_emissions with companies, rounding the totals, and returning the top 10 contributors.

**💡 Insights & Conclusions**
*Gamesa Corporación Tecnológica, S.A., a company in the renewable energy sector, surprisingly tops the list with nearly 9.8 million CO₂e, likely due to emissions from manufacturing heavy equipment like wind turbines.
*Automotive giants such as Daimler AG, Volkswagen AG, Hino Motors, and General Motors all appear in the top 10, confirming the high carbon intensity of vehicle production.
*Chemical and industrial manufacturers like Mitsubishi Gas Chemical and Arcelor Mittal also contribute significantly, underlining the energy-intensive nature of their operations.
*The presence of Lexmark and Daikin Industries suggests that electronics and appliance production also carry substantial carbon footprints.
*Overall, the top 10 companies account for a substantial portion of total emissions, highlighting how concentrated emissions can be within a few major players in heavy industry and manufacturing.
These insights can help target emissions reduction efforts at high-impact companies and sectors.

### 3.5. What are the countries with the highest contribution to carbon emissions?

```
SELECT
	 co.country_name,
	 ROUND(SUM(pe.carbon_footprint_pcf), 2) AS total_emissions
FROM
	 product_emissions pe
JOIN
	 countries co ON pe.country_id = co.id
GROUP BY
	 co.country_name
ORDER BY
	 total_emissions DESC
LIMIT 	 10;
```

| country_name | total_emissions | 
| -----------: | --------------: | 
| Spain        | 9786130.00      | 
| Germany      | 2251225.00      | 
| Japan        | 653237.00       | 
| USA          | 518381.00       | 
| South Korea  | 186965.00       | 
| Brazil       | 169337.00       | 
| Luxembourg   | 167007.00       | 
| Netherlands  | 70417.00        | 
| Taiwan       | 62875.00        | 
| India        | 24574.00        | 

**🧾 Explanation**

This query joins product_emissions with countries using country_id, sums the carbon footprint per country, rounds the values, and returns the top 10 emitting countries.

**💡 Insights & Conclusions**
* Spain leads by a large margin, contributing nearly 9.8 million CO₂e, largely influenced by emissions from Gamesa Corporación Tecnológica, which dominates product-level emissions in the dataset.
* Germany, Japan, and the USA follow, reflecting their strong industrial and automotive sectors.
* Luxembourg appears unexpectedly high, driven by specific high-emitting companies like Arcelor Mittal.
* South Korea, Brazil, and India contribute less in comparison, but still rank within the top 10.
* The geographic spread of emissions shows that while industrial powerhouses dominate, individual high-emitting companies can significantly skew national totals.
These results suggest that national emission levels in the dataset are heavily influenced by the presence of specific high-impact manufacturers, not just overall economic size.

### 3.6. What is the trend of carbon footprints (PCFs) over the years?

```
SELECT
	 year,
	 ROUND(SUM(carbon_footprint_pcf), 2) AS total_emissions
FROM
	 product_emissions
GROUP BY
	 year
ORDER BY
	 year;
```

| year | total_emissions | 
| ---: | --------------: | 
| 2013 | 503857.00       | 
| 2014 | 624226.00       | 
| 2015 | 10840415.00     | 
| 2016 | 1640182.00      | 
| 2017 | 340271.00       | 

**🧾 Explanation**

This query groups the data by year, sums the carbon_footprint_pcf for each year, rounds the result to 2 decimal places, and sorts the output chronologically to show the trend over time.

**💡 Insights & Conclusions**
* Carbon emissions peaked sharply in 2015, with over 10.8 million CO₂e, likely due to the inclusion or expansion of major high-emitting products or companies that year.
* 2013 and 2014 show moderate emissions growth, indicating a buildup toward the 2015 spike.
* After 2015, emissions dropped significantly in 2016, and even further in 2017, suggesting either improved emission efficiencies, data gaps, or changes in reporting.
* The sharp peak followed by a decline may reflect project-based data collection or operational shifts in high-emitting industries.
Overall, the trend is not linear but marked by a sharp surge and drop, highlighting the need to examine underlying causes—such as data coverage or major product introductions—that may have driven the 2015 spike.

### 3.7. Which industry groups has demonstrated the most notable decrease in carbon footprints (PCFs) over time?

```
SELECT
	ig.industry_group,
	year,
	ROUND(SUM(pe.carbon_footprint_pcf), 2) AS yearly_emissions
FROM
	product_emissions pe
JOIN
	industry_groups ig ON pe.industry_group_id = ig.id
GROUP BY
	ig.industry_group, year
ORDER BY
	ig.industry_group, year;
```

| industry_group                                                         | year | yearly_emissions | 
| ---------------------------------------------------------------------: | ---: | ---------------: | 
| "Consumer Durables, Household and Personal Products"                   | 2015 | 931.00           | 
| "Food, Beverage & Tobacco"                                             | 2013 | 4995.00          | 
| "Food, Beverage & Tobacco"                                             | 2014 | 2685.00          | 
| "Food, Beverage & Tobacco"                                             | 2015 | 0.00             | 
| "Food, Beverage & Tobacco"                                             | 2016 | 100289.00        | 
| "Food, Beverage & Tobacco"                                             | 2017 | 3162.00          | 
| "Forest and Paper Products - Forestry, Timber, Pulp and Paper, Rubber" | 2015 | 8909.00          | 
| "Mining - Iron, Aluminum, Other Metals"                                | 2015 | 8181.00          | 
| "Pharmaceuticals, Biotechnology & Life Sciences"                       | 2013 | 32271.00         | 
| "Pharmaceuticals, Biotechnology & Life Sciences"                       | 2014 | 40215.00         | 
| "Textiles, Apparel, Footwear and Luxury Goods"                         | 2015 | 387.00           | 
| Automobiles & Components                                               | 2013 | 130189.00        | 
| Automobiles & Components                                               | 2014 | 230015.00        | 
| Automobiles & Components                                               | 2015 | 817227.00        | 
| Automobiles & Components                                               | 2016 | 1404833.00       | 
| Capital Goods                                                          | 2013 | 60190.00         | 
| Capital Goods                                                          | 2014 | 93699.00         | 
| Capital Goods                                                          | 2015 | 3505.00          | 
| Capital Goods                                                          | 2016 | 6369.00          | 
| Capital Goods                                                          | 2017 | 94949.00         | 
| Chemicals                                                              | 2015 | 62369.00         | 
| Commercial & Professional Services                                     | 2013 | 1157.00          | 
| Commercial & Professional Services                                     | 2014 | 477.00           | 
| Commercial & Professional Services                                     | 2016 | 2890.00          | 
| Commercial & Professional Services                                     | 2017 | 741.00           | 
| Consumer Durables & Apparel                                            | 2013 | 2867.00          | 
| Consumer Durables & Apparel                                            | 2014 | 3280.00          | 
| Consumer Durables & Apparel                                            | 2016 | 1162.00          | 
| Containers & Packaging                                                 | 2015 | 2988.00          | 
| Electrical Equipment and Machinery                                     | 2015 | 9801558.00       | 
| Energy                                                                 | 2013 | 750.00           | 
| Energy                                                                 | 2016 | 10024.00         | 
| Food & Beverage Processing                                             | 2015 | 141.00           | 
| Food & Staples Retailing                                               | 2014 | 773.00           | 
| Food & Staples Retailing                                               | 2015 | 706.00           | 
| Food & Staples Retailing                                               | 2016 | 2.00             | 
| Gas Utilities                                                          | 2015 | 122.00           | 
| Household & Personal Products                                          | 2013 | 0.00             | 
| Materials                                                              | 2013 | 200513.00        | 
| Materials                                                              | 2014 | 75678.00         | 
| Materials                                                              | 2016 | 88267.00         | 
| Materials                                                              | 2017 | 213137.00        | 
| Media                                                                  | 2013 | 9645.00          | 
| Media                                                                  | 2014 | 9645.00          | 
| Media                                                                  | 2015 | 1919.00          | 
| Media                                                                  | 2016 | 1808.00          | 
| Retailing                                                              | 2014 | 19.00            | 
| Retailing                                                              | 2015 | 11.00            | 
| Semiconductors & Semiconductor Equipment                               | 2014 | 50.00            | 
| Semiconductors & Semiconductor Equipment                               | 2016 | 4.00             | 
| Semiconductors & Semiconductors Equipment                              | 2015 | 3.00             | 
| Software & Services                                                    | 2013 | 6.00             | 
| Software & Services                                                    | 2014 | 146.00           | 
| Software & Services                                                    | 2015 | 22856.00         | 
| Software & Services                                                    | 2016 | 22846.00         | 
| Software & Services                                                    | 2017 | 690.00           | 
| Technology Hardware & Equipment                                        | 2013 | 61100.00         | 
| Technology Hardware & Equipment                                        | 2014 | 167361.00        | 
| Technology Hardware & Equipment                                        | 2015 | 106157.00        | 
| Technology Hardware & Equipment                                        | 2016 | 1566.00          | 
| Technology Hardware & Equipment                                        | 2017 | 27592.00         | 
| Telecommunication Services                                             | 2013 | 52.00            | 
| Telecommunication Services                                             | 2014 | 183.00           | 
| Telecommunication Services                                             | 2015 | 183.00           | 
| Tires                                                                  | 2015 | 2022.00          | 
| Tobacco                                                                | 2015 | 1.00             | 
| Trading Companies & Distributors and Commercial Services & Supplies    | 2015 | 239.00           | 
| Utilities                                                              | 2013 | 122.00           | 
| Utilities                                                              | 2016 | 122.00           | 

**🧾 Explanation**

This query joins product_emissions with industry_groups, groups the data by industry and year, calculates the total carbon emissions per industry per year, and orders the results to trace emission trends over time.

**💡 Insights & Conclusions**
* Technology Hardware & Equipment shows a steep decline from 167,361 CO₂e (2014) to just 1,566 CO₂e (2016) before rebounding in 2017. This suggests a temporary shift—possibly production outsourcing or cleaner technologies.
* Software & Services peaked at 22,856 CO₂e (2015), then dropped drastically to 690 CO₂e (2017). This may reflect efficiency improvements or reporting changes.
* Media saw a steady drop from 9,645 CO₂e (2013–2014) to 1,808 CO₂e (2016), suggesting reduced emissions intensity or downsizing.
* Food, Beverage & Tobacco emissions plummeted to 0 CO₂e in 2015, surged in 2016, and then dropped again in 2017 — a volatile trend possibly tied to inconsistent data or specific product records.
* Capital Goods had a dramatic drop from 93,699 CO₂e (2014) to 3,505 CO₂e (2015) and later climbed again, suggesting a temporary production shift or operational restructuring.
Several industries, notably Tech Hardware, Software, and Media, exhibit sharp emission reductions over specific years. While this may reflect real progress (e.g., tech efficiency gains or changes in sourcing), some drops are likely influenced by data completeness, temporary shifts in operations, or methodological changes. Sustained declines—if validated—point to industries leading in decarbonization or adapting cleaner production techniques.
