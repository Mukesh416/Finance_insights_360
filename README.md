# Finance_insights_360

Power BI
3. Data collection and exploration
3.1. Data warehouse, OLAP vs OLTP, Data Catalog
	Data warehouse
	 
	Perform ETL  Store them in a warehouse  Perform Data Analytics
	Warehouses ex. MySql , relational database, NoFlag
	 
	OLAP (online analytical processing) and OLTP (online transaction processing) serves the purpose of database management.
•	OLAP: OLAP databases are designed for complex queries and data analysis. They are optimized for read-heavy operations and are used to support decision-making processes by providing users with access to historical, summarized, and aggregated data.
•	OLTP: OLTP databases are designed for transactional processing. They are optimized for write-heavy operations and are used to support day-to-day business operations, such as order processing, inventory management, and customer interactions. These are machine critical i.e, we cannot afford slow or error in these areas.

OLAP databases are designed for analytical and reporting tasks, while OLTP databases are designed for transactional processing and day-to-day business operations. The choice between OLAP and OLTP depends on the specific requirements of an organization's data needs. In some cases, hybrid systems or data warehousing solutions are used to combine the strengths of both OLAP and OLTP databases for comprehensive data management.

Data catalog:
A data catalog is a centralized repository or metadata management system that helps organizations organize, discover, and manage their data assets. It acts as a catalog or index for all data assets within an organization, providing information about data sources, datasets, data definitions, data lineage, and more. Data catalogs play a crucial role in data governance, data management, and data-driven decision-making processes.
3.2 MySql

4 Install Power BI and load data from MySql
	Connect sql server to Power BI and load data from sql to BI
	Incase of understanding schema you can see the file”atliq-data-catalog”


	
	

4.1. load .sql files to Database

4.2. create date table
Formulae to create date		
= {Number.From(#date(2017,9,1))..Number.From(#date(2022,12,31))}
Apply changes that will be reflected in power BI desktop 

4.3. Data Validation
You do validate table data with the values mentioned in file”benchmark_numbers”
Preliminary data validation with benchmark numbers is the most important once data is accessed in Power BI
5. Data Transformation in power query
5.2. Transform data in Power Query
Created new table called Remaining forecast from the reference of gdb041 fact_forecast_monthly table where the date is filtered after current month. Here the current month is the last date on which the sale is happened in gdb041 fact_sales_monthly table.
Note : To get last date I used  = List.Max(#"gdb041 fact_sales_monthly"[date])

I have details of actual sold quantity in gdb041 fact_sales_monthly table and future estimates in Remaining forecast table. So next step is to combine them both. Use append Queries as new in the home section and insert both the tables.
Note: the sold quantity/ future sale estimate are the names of sales in both table. Both should be renamed as keep under same name to avoid split of columns in newly formed table.
5.3. Creating calculated columns as per the Business requirement
Task 1 : Now I have to include fiscal year in fact_Actual_estimantes. The challenge here is I don’t have any index to bring the fiscal year from gdb056 gross_sales table.
Now, I have to find the total cost of each product segment i.e., gross price by doing left outer join from the option Merge Queries in the Home section.
Later I multiplied the resultant cost with the Quantity(Qty) which gives us gross_sales_amount
Task 2 : Here I want the pre_invoice discount from pre_invoice_deduction table
Use Merge query  Table fact_actual_estimates and gdb056 pre_invoice_deductions using left outer join.
Note: Only insert the required columns from the merge output. For example you need only “pre_invoice_discount_pct” columns. Same applies for the above task.
Do appropriate calculation to exclude discount percentage of pre_invoice and show the resultant amount i.e., net_invoice_sales_amount
Note : while performing all these steps use the best practices like
Naming query steps
Grouping tables
Disable load for some tables to improve performance: Here you uncheck the remaining forecast, lastSalesMonth table so that you skip the unnecessary loading
Table naming conventions
6.Data Modelling and calculated columns
Task1: establish the connection between tables before doing that try to include only necessary columns in each table to eliminate the confusion.
Task2: Writing a DAX expression in power BI
Note: Inside power query editor we use “M Language”
Note: Outside power query i.e., Power BI, we use “DAX – Data Analysis Expressions”
DAX vs power query: 

Task 3 : post invoice deduction in Power BI – using DAX formula
First establish h the connection between tables
Then write the DAX code
a.)	To get the percentage of Post invoice deductions we create new column and use

post_invoice_deductions_amount = 
CALCULATE(MAX(post_invoice_deductions[discounts_pct]), 
RELATEDTABLE(post_invoice_deductions))

This will get discount_pct from post invoice deducitons table. 
b.)	To get the percentage amount we use
post_invoice_deductions_amount = 
var res = CALCULATE(MAX(post_invoice_deductions[discounts_pct]), 
RELATEDTABLE(post_invoice_deductions))
return res*fact_actuals_estimates[net_invoice_sales_amount]
c.)	Freight cost
freight cost = 
var res = CALCULATE(MAX(freight_cost[freight_pct]), 
RELATEDTABLE(freight_cost))
return res*fact_actuals_estimates[Net_sales_amount]

d.)	manufacturing cost = 
var res = CALCULATE(MAX(manufacturing_cost[manufacturing_cost]), 
RELATEDTABLE(manufacturing_cost))
return res*fact_actuals_estimates[Qty]
e.)	other cost = 
var res = CALCULATE(MAX(freight_cost[other_cost_pct]), 
RELATEDTABLE(freight_cost))
return res*fact_actuals_estimates[Net_sales_amount]

f.)	COGS = 








