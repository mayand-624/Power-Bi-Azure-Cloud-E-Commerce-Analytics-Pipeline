# Azure Cloud E-Commerce Analytics Pipeline

### Dashboard Link : https://app.powerbi.com/groups/21528a50-7f10-4549-8bd4-65381460072b/reports/1706eba9-efcf-4c7b-baa0-3f6723b48dd3/c495fdab7be4693a09bf?experience=power-bi

## Problem Statement

This project simulates a modern retail data engineering workflow. The primary focus of this deployment was establishing a fully managed Azure SQL Database to stage, clean, and analyze raw e-commerce apparel data.

A significant challenge in modern retail analytics is managing inconsistent or corrupted data feeds before they reach the visualization layer. This project tackles that by configuring cloud firewall security for localized ingestion, executing precise SQL-based string manipulation to repair corrupted pricing strings, and utilizing advanced Power Query conditional logic to impute missing financial values.

Finally, the engineered semantic model powers a dynamic, multi-page Power BI dashboard designed to analyze brand performance, profit margins, and discount strategies, equipping stakeholders with accurate, reverse-engineered financial metrics.

## Repository Assets

This repository contains the complete backend and frontend architecture required to reproduce this pipeline:

- `Dataset/`: `Men_Tshirt_Raw.csv` (Sample raw e-commerce data allowing for full project reproduction).
- `SQL_Scripts/`: `Azure_Data_Cleaning.sql` (T-SQL scripts detailing string manipulation and data type conversions).
- `PowerQuery/`: `Conditional_Imputation.m` (M-code script detailing the 'NA' filtering and 1.5x price imputation logic).
- `Report/`: `Azure_Ecommerce_Analytics.pbix` (The final interactive BI dashboard).

## Steps Followed

- Step 1 : **Cloud Infrastructure Setup (Azure SQL):** Deployed a fully managed Azure SQL Database server to act as the primary enterprise data warehouse[cite: 10].
- Step 2 : Configured strict server-level IP firewall rules within the Azure portal, whitelisting specific Client IPv4 addresses to allow secure, localized connections from SQL Server Management Studio (SSMS) for initial data ingestion[cite: 10].
- Step 3 : **SQL Data Cleansing (T-SQL):** Profiled the raw dataset in the Azure environment and identified data corruption within the pricing columns, specifically the presence of rogue question marks (`?`) masking the numeric values[cite: 11].
- Step 4 : Executed nested T-SQL string manipulation functions directly within the Azure database to repair the corrupted pricing data[cite: 11]. The `TRIM`, `REPLACE`, and `CAST` functions were layered to strip the unwanted characters and convert the strings into workable formats:
    
    ```sql
    UPDATE [dbo].[Men+Tshirt]
    SET original_price = TRIM(REPLACE(CAST(original_price as VARCHAR(MAX)), '?', ''))
    WHERE original_price LIKE '%?%';
    ```
    

<img width="1437" height="595" alt="SS1" src="https://github.com/user-attachments/assets/066a4311-4e23-4bcd-a08c-b72861fbb91a" />


- Step 5 : **Dynamic Data Imputation (Power Query):** Connected Power BI to the Azure SQL Database and utilized Power Query to handle severe missing financial data. Rows where both the original price and sale price were missing (`NA`) were completely filtered out of the dataset to prevent corrupted averages[cite: 11].
- Step 6 : Addressed rows where the 'Sale Price' was intact but the 'Original Price' was documented as `NA`. To maintain dataset volume, multi-step conditional logic was built to impute the missing original values[cite: 11]. A temporary 'Factor' column was created, applying a 150% markup (`1.5`) to the existing Sale Price (`Sale_Price * 1.5`), effectively engineering a new, cleansed `Marked Price` column[cite: 11, 12].

<img width="1142" height="487" alt="SS2" src="https://github.com/user-attachments/assets/ede2a658-d631-42ee-aff7-f5e71e9cd5c8" />


- Step 7 : **Financial Reverse-Engineering (DAX):** Transitioned to the DAX modeling layer to build the foundational financial metrics. A `Discount %` column was generated to evaluate standard retail markdowns[cite: 12].
- Step 8 : Engineered a dynamic financial model by generating a randomized `Profit %` utilizing the `RANDBETWEEN(2, 17)` DAX function to simulate realistic apparel profit margins ranging from 2% to 17%[cite: 12].
- Step 9 : Algebraically reverse-engineered the foundational base `Cost Price` for every product using the `DIVIDE` function against the final sale price and the simulated profit margin[cite: 12]:
    
    ```
    Cost Price = DIVIDE(100 * 'Men+Tshirt'[Sale_Price], 100 + 'Men+Tshirt'[Profit %])
    ```
    
- Step 10 : Designed a professional two-page UI. *Page 1 (Brands)* focuses on high-level navigation using an embedded Multi-Row Card over a branded backdrop. *Page 2 (Details)* houses the core analytics[cite: 13].
- Step 11 : Integrated a custom **'Scroller'** visual at the top of the Details page to dynamically stream the company title, mirroring a modern stock-ticker or live retail feed[cite: 13].

# Dashboard Snapshots

### Page 1: Exclusive Brands Collection

<img width="1317" height="742" alt="Page1" src="https://github.com/user-attachments/assets/5a262c61-e39b-4a3f-98f3-0e00e0cd2cda" />


### Page 2: Analytics & Details

<img width="1326" height="737" alt="Page2" src="https://github.com/user-attachments/assets/9b47a7f1-3530-4fcb-82f2-55eb75a05f03" />


# Insights

A comprehensive e-commerce financial reporting pipeline was built on Azure cloud infrastructure and deployed via Power BI.

Following inferences can be drawn from the dashboard:

### [1] Data Integrity & Pipeline Resiliency

```
By pushing string manipulation (T-SQL) upstream to the Azure SQL Database and handling null imputation conditionally via Power Query, the downstream DAX engine operates exclusively on clean, verified numerical data. This completely eliminates UI crashes caused by corrupted string types entering mathematical formulas.
```

### [2] Discount vs. Profitability Margins

```
The reverse-engineered financial model allows analysts to directly contrast the Top Brands by Average Discount against the Top Brands by Highest Average Profit. This highlights which apparel brands rely on heavy markdowns to drive volume versus those that maintain premium, full-price sales strategies with 15-17% profit ceilings.
```

### [3] Product Portfolio Diversification

```
The Donut and Bar chart categorizations break down the Highest Number of Varieties and Average Sales Price by Brand. This allows procurement teams to easily identify which manufacturers dominate the product catalog and assess if capital allocation is disproportionately tied to a single supplier.
```
