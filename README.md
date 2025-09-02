# Cafe Sales Data Analysis ☕


## Project Overview

This project, based on the Cafe Sales Dataset focused on extensive cleaning and transforming before meaningful analysis and visualizations could be made. The source of the dataset is "dirty_cafe_sales.csv", and the clean version is "cafe_cleaned".

## Project Objective
The goal of this project was to transform the raw and dirty dataset into a clean and usable dataset for analysis. From which, interactive Power BI dashboards can be build to identify data quality gaos and sales insights.

## Approach
### Data Cleaning/Preparation
- Standardize missing values, data types and errors
- Recalculate missing values: `Price Per Unit` , `Quality`,`Total Spent`
- Remove invalid and blank rows: `Transaction Date`
- Add data quality flags for data completeness: `Location`, `Payment Method`

## Data Cleaning Process

The original cafe sales dataset presented issues such as errors, missing values and inconsistencies in the records. To ensure the data is ready for analysis, I employed the following data cleaning steps using Power BI Power Query.

### **Standardizing Missing Values** ✔️
- Replaced all `unknown`, `error`, and blank entries with `NULL` in all fields.
- Introduced a "Not Provided" data  flag for missing values in the `Payment Method` field and "Not Specified" for `Location` field for consistency.

### **Removing Invalid Records** ✖️
- Filtered out rows with missing/invalud `Transaction Date`- these would not support time-based analyses
  

### **Data Cleaning & Recalculated Columns** 🧹

In the preprocessing stage in Power Query, using M language, I created custom columns in the case of missing or inconsistent values in the `Quantity`, `Price_Per_unit` and `Total_Spent`fields.

1.  **Recalculating Quantity**
---
``` m
Recalculated_Quantity =
  IF ( [Quantity] = null
       and [Total Spent] <> null
        and [Price Per Unit] <> null
        and [Price Per Unit] <> 0
)
then ([Total Spent] / [Price Per Unit])
else [Quantity])
```

   
2. **Recalculating Price Per Unit**
---
For the rows that had missng `Price_Per_Unit` but contained both `Quantity` and `Total_Spent`, I recovered the values using the following formula:

``` m
Recalculated_PricePerUnit =
IF (
    [Price Per Unit] = null 
       and [Quantity] <> null 
       and [Total Spent] <> null 
       and [Quantity] <> 0
)
then ([Total Spent] / [Quantity])
else [Price Per Unit]
```

3. **Recalculating Total Spent**
---
Having recalculated the missing values from the `Price_Per_unit` and `Quantity` fields, I used the recalculated values to determine the related `Total Spent` using this formula:

``` m
Recalculated_TotalSpent = 
    IF (
        [Total Spent] = null and 
        [Recalculated_Quantity] <> null and 
        [Recalculated_PricePerUnit] <> null
    ) 
    then ([Recalculated_Quantity] * [Recalculated_PricePerUnit]) 
    else [Total Spent]

```

### Handling data types and adding quality flags
- Converted fields; `Quantity`, `Price_Per_Unit` to a whole number and currency data type respectively.
- Added a custom column `Data_Completeness_Flag` for `Location` and `Payment Method` fields, which although incomplete, held substantial value in sales analysis.
   - From the initial data flags created, I used the following formula:
     
  ``` m
     Data_Completeness_Flag = 
    IF ([Payment Method] = "Not Provided" and [Location] = "Not Specified") then "Both Missing"
         else if [Payment Method] = "Not Provided" then "Missing Payment Method"
         else if [Location] = "Not Specified" then "Missing Location"
   else "Complete"

     ```

## Exploratory Data Analysis 
#### Key Insights
The key insights from the EDA process of the cafe data sales analysis sought to identify:
- Sales revenue trends to identify seasonal demand
- Product performance: top and lowest selling products
- Preferred payment methods
- Location analysis: top and lowest performing branches
- Data quality gaps: quantifying the impact of incomplete data eg. misssing loacations on reporting accuracy.

## Tools Used 
- Power BI Power Query- Data cleaning
- Power BI- DAX calculations, Dashboard building

## Outcome

The final dataset is clean, consistent, traceable and ready for use for accurate analysis and reporting

