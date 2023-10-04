Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries: 
SELECT city, country,totaltransactionrevenue FROM (
SELECT  city, country,totaltransactionrevenue, RANK() OVER (PARTITION BY COUNTRY ORDER BY totaltransactionrevenue DESC) 
FROM (
SELECT city, country, COALESCE(SUM(totaltransactionrevenue),0) AS totaltransactionrevenue
FROM public.all_sessions_clean
GROUP BY 1,2
)
WHERE totaltransactionrevenue <> 0
) WHERE RANK = 1
ORDER BY 3 DESC;



Answer:
city	country	totaltransactionrevenue
N/A	United States	6092.56
Tel Aviv-Yafo	Israel	602
Sydney	Australia	358
Toronto	Canada	82.16
Zurich	Switzerland	16.99




**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries: 
SELECT total_ordered/CNT AS AVG_ORDERED, city, country
FROM (
SELECT SUM(CAST(B.total_ordered AS integer)) AS total_ordered, COUNT(A.fullvisitorid) AS CNT, city, country
FROM all_sessions_clean A
LEFT OUTER JOIN sales_report_raw B
ON A.productsku = B.productsku
WHERE B.total_ordered IS NOT NULL
GROUP BY 3,4
);



Answer: If we try to get average by each city and country it gives a huge dataset, I have added its output as Answer2.csv





**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries: 
SELECT * FROM (
SELECT country,v2productcategory, total_ordered, DENSE_RANK() over(partition by country order by total_ordered desc) AS RN
 FROM (
SELECT country,v2productcategory,count(total_ordered) AS total_ordered
FROM all_sessions_clean A
JOIN sales_report_raw B
ON A.productsku = B.productsku
group by 1,2
)
WHERE rn = 1
ORDER BY 3 DESC



Answer: "v2productcategory" "Home/Apparel/Men's/Men's-T-Shirts/" is the most ordered category in most of the countries. It was not feasible to get data by city as it has many values 'not set' which might not give correct data




**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:

SELECT * FROM (
SELECT country,v2productname,v2productcategory,total_ordered, DENSE_RANK() over(partition by country order by total_ordered desc) AS RN
 FROM (
SELECT country,v2productname,v2productcategory,count(total_ordered) AS total_ordered
FROM all_sessions_clean A
JOIN sales_report_raw B
ON A.productsku = B.productsku
group by 1,2,3
))
WHERE rn = 1
ORDER BY 4 DESC


Answer:

"v2productname" "22 oz YouTube Bottle Infuser" is the top selling product in most countries



**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:

SELECT sum(totaltransactionrevenue),city,country
FROM all_sessions_clean 
WHERE totaltransactionrevenue>0
GROUP BY city,country
ORDER BY 1 desc

Answer: The major consumer base for this site is United States







