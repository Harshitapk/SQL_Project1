What issues will you address by cleaning the data?
Firstly create new tables from raw data and apply cleaning steps on new tables

1. Get distinct values in tables
select count(distinct sku) from products_raw
select count(*) from products_raw
If both gives same count then sku can be primary key and confirms the uniqueness across rows.
similarly check and try to get column with distinct values across rows in other tables

2. Change data type accordingly
e.g. 
ALTER TABLE all_sessions_clean
ALTER COLUMN productrevenue type numeric(20,2)
USING productrevenue::numeric(20,2)

update all_sessions_clean set productrevenue=(productrevenue/1000000)

ALTER TABLE all_sessions_clean
ALTER COLUMN date type date
USING date::date

3. check for empty columns and drop the column if they are not necessary
select distinct columnname from tablename -- if column is empty and irrelevant,drop it
alter table all_sessions_clean drop column itemrevenue

Below columns were dropped
itemrevenue - all_sessions_clean
itemquantity - all_sessions_clean
time - all_sessions_clean
userid - analytics_clean

4. Update consistent values where data is not set
---- updated city and country missing values as 'N/A'
update all_sessions_clean set city = replace (city,'(not set)','N/A') 
where city='(not set)'
update all_sessions_clean set country= replace (country,'(not set)','N/A')
where country='(not set)'


select v2productname,v2productcategory from all_sessions_clean 
where v2productcategory='(not set)' and v2productname like '%Men%' 

  ---- Need to update categories for many rows where it was 'not set' or had weird name
update all_sessions_clean set v2productcategory = replace (v2productcategory,'(not set)','Home/Lifestyle/')
where v2productcategory='(not set)' and v2productname like '%Mat%'

--- Updated value for timeonsite to calculate time as it had null values
update all_sessions_clean set timeonsite=0 where timeonsite is null


