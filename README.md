# Adventure-Works-Power-BI-Project

This repository contains a Power BI project based on the Adventure Works dataset. The dataset includes several CSV files with various data points on sales, customers, products, and returns. This project involves data cleaning, modeling, and creating DAX measures for analytical insights.

# Dataset Overview

The project includes the following CSV files:

- **Calendar Lookup:** Contains a single column, `Date`.
- **Customer Lookup:** Columns include `CustomerKey`, `Prefix`, `FirstName`, `LastName`, `BirthDate`, `MaritalStatus`, `Gender`, `EmailAddress`, `AnnualIncome`, `TotalChildren`, `EducationLevel`, `Occupation`, `HomeOwner`.
- **Product Category:** Columns include `ProductCategoryKey`, `CategoryName`.
- **Product Lookup:** Columns include `ProductKey`, `ProductSubcategoryKey`, `ProductSKU`, `ProductName`, `ModelName`, `ProductDescription`, `ProductColor`, `ProductSize`, `ProductStyle`, `ProductCost`, `ProductPrice`.
- **Product Subcategory:** Columns include `ProductSubcategoryKey`, `SubcategoryName`, `ProductCategoryKey`.
- **Returns Data:** Columns include `ReturnDate`, `TerritoryKey`, `ProductKey`, `ReturnQuantity`.
- **Territory Lookup:** Columns include `SalesTerritoryKey`, `Region`, `Country`, `Continent`.
- **Sales Data 2020:** Columns include `OrderDate`, `StockDate`, `OrderNumber`, `ProductKey`, `CustomerKey`, `TerritoryKey`, `OrderLineItem`, `OrderQuantity`.
- **Sales Data 2021:** Columns include `OrderDate`, `StockDate`, `OrderNumber`, `ProductKey`, `CustomerKey`, `TerritoryKey`, `OrderLineItem`, `OrderQuantity`.
- **Sales Data 2022:**  Columns include `OrderDate`, `StockDate`, `OrderNumber`, `ProductKey`, `CustomerKey`, `TerritoryKey`, `OrderLineItem`, `OrderQuantity`.

# Data Cleaning

In Power Query, data cleaning operations were performed as follows:

 - **Customer Lookup:** Applied steps such as promoted headers, changing types, merging columns, capitalizing each word, inserting age, adding division and rounding, renaming, reordering columns, replacing values, removing errors, and duplicates.
 - **Product Lookup:** Steps included promoted headers, changing types, and renaming columns.
 - **Product Subcategory and Product Category:** Only promoted headers, changing types, and renaming columns.
 - **Territory Lookup:** Promoted headers, changing types, text trimming, and cleaning.
 - **Calendar Lookup:** Promoted headers, changing types, adding columns for year, month, month name, quarter, start of the week, start of the month, and inserting conditional columns, with columns reordered accordingly.
Finally, the sales data tables (2020, 2021, 2022) were merged using Append Queries and renamed as **FactSales**.


# Data Model

The data model connects various tables through relationships:
  
 - `Calendar Lookup[Date]` ➔ `FactSales[OrderDate]`
 - `Customer Lookup[CustomerKey]` ➔ `FactSales[CustomerKey]`
 - `Product Lookup[ProductKey]` ➔ `FactSales[ProductKey]`
 - `Territory Lookup[SalesTerritoryKey]` ➔ `FactSales[TerritoryKey]`
 - `Product Category[ProductCategoryKey]` ➔ `Product Subcategory[ProductCategoryKey]`
 - `Product Subcategory[ProductSubcategoryKey]` ➔ `Product Lookup[ProductSubcategoryKey]`
 - `Calendar Lookup[Date]` ➔ `Returns Data[ReturnDate]`
 - `Customer Lookup[CustomerKey]` ➔ `Returns Data[CustomerKey]`
 - `Territory Lookup[SalesTerritoryKey]` ➔ `Returns Data[TerritoryKey]`

![ss5](https://github.com/user-attachments/assets/564d9016-9921-4151-9baa-cf8d9389c662)


# Additional Tables Created

The following new tables were created:

  **1. Customer Matrix:** Provides an overview of key metrics related to customers.

`Customer_Metrics = {
    ("Total Customers", NAMEOF('_Measure'[Total Customers]), 0),
    ("Avg. Revenue Per Customer", NAMEOF('_Measure'[Avg. Revenue Per Customer]), 1)
}`

  
  **2. Price Adjustment (%):** A table that applies specific price adjustments to products.

`Price Adjustment (%) Value = SELECTEDVALUE('Price Adjustment (%)'[Price Adjustment (%)], 0)`

  **3. Product Matrix:** Provides metrics related to products, including orders, returns, and revenue.
  
`Product_Metrics = {
    ("Orders", NAMEOF('_Measure'[Total Orders]), 0),
    ("Returned", NAMEOF('_Measure'[Orders Returned]),1),
    ("Return Rate (%)", NAMEOF('_Measure'[Return Rate]), 2),
    ("Revenue", NAMEOF('_Measure'[Total Revenue]), 3),
    ("Gross Profit", NAMEOF('_Measure'[Gross Profit]), 4),
    ("Profit Margin (%)", NAMEOF('_Measure'[Profit Margin]), 5)
}`


# DAX Measures

The following DAX measures were created to drive insights and calculations:

  **1. Adjusted Price:**

`
Adjusted Price = [Avg Retail Price] * (1+ 'Price Adjustment (%)'[Price Adjustment (%) Value])`

 **2. Adjusted Profit:**

`
Adjusted Profit = [Adjusted Revenue] - [Total Cost]`

 **3. Adjusted Revenue:**

`
Adjusted Revenue = SUMX('Fact Sales', 'Fact Sales'[OrderQuantity] * [Adjusted Price])`

 **4. Avg Order Value:**

`
Avg Order Value = DIVIDE([Total Revenue],[Total Orders])`

  **5. Avg Retail Price:**
  

`Avg Retail Price = AVERAGE('Product Lookup'[Product Price])`

  **6. Avg Revenue Per Customer:**
  
`
Avg Revenue Per Customer = DIVIDE('_Measure'[Total Revenue], [Total Customers])`


  **7. Bike Sold:**
  
`
Bike Sold = CALCULATE( [Total Quantity], 'Product Categories Lookup'[Category Name] = "Bikes")`

  **8. Gross Profit:**
  
`
Gross Profit = [Total Revenue] - [Total Cost]`

  **9. Order Target:**
  
`
Order target = [Previous Month Order] * 1.1`


  **10. Order Target Gap:**
  
`
Order Target Gap = [Total Orders] - [Order target]`

  **11. Orders Returned:**
  
`
Orders Returned = COUNTROWS('Returns Data')`

  **12. Previous Month Order:**
  
`
Previous Month Order = CALCULATE([Total Orders], DATEADD('Calendar Lookup'[Date],-1,MONTH))`

  **13. Previous Month Return:**
  
`
Previous Month Return = CALCULATE([Return Rate], DATEADD('Calendar Lookup'[Date],-1,MONTH))`

  **14. Previous Month Revenue:**
  
`
Previous Month Revenue = CALCULATE([Total Revenue], DATEADD('Calendar Lookup'[Date], -1,MONTH))`

  **15. Profit Margin:**
  
`
Profit Margin = DIVIDE([Gross Profit],[Total Revenue])`

  **16. Quantity Returned:**
  
`
Quantity Returned = SUM('Returns Data'[ReturnQuantity])`

  **17. Return Rate:**
  
`
Return Rate = DIVIDE([Orders Returned],[Total Orders])`


  **18. Revenue Target:**
  
`
Revenue Target = [Previous Month Revenue] * 1.1`

  **19. Revenue Target Gap:**
  
`
Revenue Target Gap = [Total Revenue] - [Revenue Target]`

  **20. Total Cost:**
  
`
Total Cost = SUMX('Fact Sales', 'Fact Sales'[OrderQuantity] * RELATED('Product Lookup'[Product Cost]))`

  **21. Total Customers:**
  
`
Total Customers = DISTINCTCOUNT('Fact Sales'[CustomerKey])`


  **22. Total Orders:**
  
`
Total Orders = DISTINCTCOUNT('Fact Sales'[OrderNumber])`


  **23. Total Quantity:**
  
`
Total Quantity = SUM('Fact Sales'[OrderQuantity])`


  **24. Total Revenue:**
  
`
Total Revenue = SUMX('Fact Sales', 'Fact Sales'[OrderQuantity] * RELATED('Product Lookup'[Product Price]))`


  **25. Total Revenue YoY%:**

`
Total Revenue YoY% = VAR __PREV_YEAR = CALCULATE([Total Revenue], DATEADD('Calendar Lookup'[Date], -1, YEAR)) RETURN DIVIDE([Total Revenue] - __PREV_YEAR, __PREV_YEAR)`

# Visualizations

The project includes a variety of visualizations created in Power BI to display sales trends, customer metrics, product performance, and other key insights.

### Executive Dashboard
---

![ss1](https://github.com/user-attachments/assets/1beaaaeb-f39d-4257-a2cc-e737f76e4b85)

### Map
---

![ss2](https://github.com/user-attachments/assets/8489e2ad-9fa6-4a26-b10a-5d66cd0bcb4f)

### Customer Details 
---

![ss3](https://github.com/user-attachments/assets/d33ac5b2-54c3-4bab-9a9b-554a664d7acf)

### Product Details 
---

![ss4](https://github.com/user-attachments/assets/930a3ae6-2cbf-4108-8e99-682eda5cdeca)


This README file now thoroughly documents the project setup, tables, data cleaning steps, created tables, DAX measures, and overall objectives for easy reference and collaboration. Let me know if you'd like to add more sections!




