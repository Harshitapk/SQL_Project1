What are your risk areas? Identify and describe them.
There were issues with data types and defining appropriate data types for each column
Many columns had missing value which could conclude incorrect information
Assumption on relation between different tables


QA Process:
Describe your QA process and include the SQL queries used to execute it.
--Check details about column using information_schema.columns for appropriate datatype

--Confirm distinct value in sku column of products table
select count(distinct sku) from products_clean
select count(*) from products_clean

--Check missing data in columns considered in decision making

--Update column value for consistent data if required:

update all_sessions_clean set city = replace (city,'(not set)','N/A’) 
      where city='(not set)’

update all_sessions_clean set country= replace (country,'(not set)','N/A’)
     where country='(not set)'

select v2productname,v2productcategory from all_sessions_clean 
     where v2productcategory='(not set)' and v2productname like ‘%Mat%' 

update all_sessions_clean set v2productcategory = replace (v2productcategory,'(not set)','Home/Lifestyle/’)
     where v2productcategory='(not set)' and v2productname like '%Mat%'

update all_sessions_clean set timeonsite=0 where timeonsite is null

ALTER TABLE all_sessions_clean ALTER COLUMN date type date USING date::date


