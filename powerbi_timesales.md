## Times Sales Data - Interactive PowerBI Report

**Project description:** This PowerBI project was created using the sample data set 'Plant_DTS.xlsx' and aims to provide a finished report capable of providing high value insights into the example companies' KPIs. The data set includes product types, country, revenues, cost of goods sold, and quantity sold.

### 1. Transforming Data in Power Query

The first step in creating our report is to perform any necessary transformations in our data within Power Query. During this step we look at handling missing data, errors, recoding variables, reformating data types when necessary, and delete any unneeded columns.

<img src="images/powerquery.PNG?raw=true"/>

### 2. Establishing relationships between data tables

The next step is to establish relationships between our data tables using the unique identifiers. In our data, our unique identifiers are product_id and account_id. 

<img src="images/relations tables.PNG?raw=true"/>

### 3. Creating calculated measures and switches

In this step we create a new table to contain calculated measures, which will be our KPIs used in the report. Some calculations are quite simple, such as gross profit where we simply use the formula [Sales_USD] - [COGS_USD]. Others we will need to create switch variables, which allow our final report to have interactive capabilities. For this, we will need to use DAX. An example for the variable YTD (Year to Date) is provided below:

```
YTD = 
VAR selected_value = SELECTEDVALUE(Slicer_Values[Values])
VAR result = SWITCH(selected_value,
    "Sales", [YTD_Sales],
    "Quantity", [YTD_Quantity],
    "Gross Profit", [YTD_GrossProfit],
    BLANK()
)
RETURN
result
```

<img src="images/measures.PNG?raw=true"/>

### 4. Building report visuals

Once we have created all of our KPI measures and switches, we proceed to creating our visuals. For our visuals, I have included KPI cards, slicers, a waterfall chart, a treemap, a line and stacked column chart, and a scatter plot.


### 5. Final result

Our final result is an interactive report which allows rapid viewing of KPIs based on the year selected, which can be broken down further by month, product type, and country. This enables the end user to understand strengths and weaknesses in the company's performance at a glance, which can provide great insight for further analysis.

<img src="images/powerbi time sales report.PNG?raw=true"/>

<img src="images/final result.PNG?raw=true"/>
