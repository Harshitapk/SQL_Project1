Question 1: How many visitors visited 1 or more products?

SQL Queries:

---- It gives row_num to each visit of each visitor

WITH visitinfo as(SELECT v2productname,fullvisitorid ,  
ROW_NUMBER() OVER (partition by fullvisitorid) as row_num
from all_sessions_clean),

---- It counts row_num of each visitor
 
visits as(                                              
SELECT count(row_num)as CNT,fullvisitorid FROM visitinfo
GROUP BY fullvisitorid
)

---- It tells about how many visitors visit certain no. of products

SELECT COUNT(*) as "Number of visitors", CNT as "Number of Products" FROM visits    ---- It tells about how many visitors visit certain no. of products
GROUP BY 2
ORDER BY 1 desc 

Answer: Around 13431 visitors visited only 1 product
13431	1
713	2
63	3
10	4
2	6
2	8
1	10
1	7


Question 2: Top 10 countries from where visitor number is highest

SQL Queries:

select COUNT(fullvisitorid) as VISITOR_COUNT,COUNTRY  FROM public.all_sessions_clean
GROUP BY country
ORDER BY 1 desc limit 10

Answer:
8725	"United States"
719	"India"
668	"United Kingdom"
642	"Canada"
336	"Germany"
241	"Japan"
225	"Australia"
218	"France"
174	"Taiwan"


Question 3: As highest number of visitors are from US, which brand is most famous?

SQL Queries:
SELECT count(*),v2productcategory FROM all_sessions_clean
WHERE v2productcategory like '%Shop by Brand%' and country='United States'
GROUP BY v2productcategory
ORDER BY 1 desc 

Answer: YouTube is the most famous brand in US

"count"	"v2productcategory"
808	"Home/Shop by Brand/YouTube/"
547	"Home/Shop by Brand/Google/"
186	"Home/Shop by Brand/Android/"
76	"Home/Shop by Brand/"
5	"Home/Shop by Brand/Waze/"


Question 4: Which category of clothing is visited most?

SQL Queries:
(SELECT count(*) as Men_count,CASE WHEN v2productcategory like '%Apparel/Men''s%' THEN 'Men''s' END AS v2productcategory FROM all_sessions_clean
WHERE v2productcategory like '%Apparel/Men''s%' and country='United States'
GROUP BY CASE WHEN v2productcategory like '%Apparel/Men''s%' THEN 'Men''s' END
ORDER BY 1 desc ) 

----- It gives total count for products visited in Men's apparel

UNION ALL

(SELECT count(*) as Women_count,CASE WHEN v2productcategory like '%Apparel/Women''s%' THEN 'Women''s' END AS v2productcategory FROM all_sessions_clean
WHERE v2productcategory like '%Apparel/Women''s%' and country='United States'
GROUP BY CASE WHEN v2productcategory like '%Apparel/Women''s%' THEN 'Women''s' END
ORDER BY 1 desc )   

----- It gives total count for products visited in Women's apparel

UNION ALL
(SELECT count(*) as Kids_count,CASE WHEN v2productcategory like '%Apparel/Kid''s%' THEN 'Kid''s' END AS v2productcategory FROM all_sessions_clean
WHERE v2productcategory like '%Apparel/Kid''s%' and country='United States'
GROUP BY CASE WHEN v2productcategory like '%Apparel/Kid''s%' THEN 'Kid''s' END
ORDER BY 1 desc)  

----- It gives total count for products visited in Kid's apparel


Answer: Men's wear is visited most on this website

"product_count"	"v2productcategory"
659	"Women's"
229	"Kid's"
1767	"Men's"



Question 5: Visitors from which country spend most time on this website

SQL Queries:

 ----- It gives total time spent by each visitor

WITH total_t as
(SELECT sum(timeonsite)/60 as total_time,fullvisitorid FROM all_sessions_clean 
GROUP BY fullvisitorid
ORDER BY 1 desc )

----- It gives total time spend by visitors from each country limited by top 10

SELECT sum(total_time),country FROM all_sessions_clean ac       
join total_t tt on ac.fullvisitorid=tt.fullvisitorid
GROUP BY country
ORDER BY 1 desc limit 10


Answer: Visitors from United States spend most time on this site



