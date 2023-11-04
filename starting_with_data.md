Question 1: Which countries do the top 10 people who visit this site come from?

SQL Queries:

WITH cte as (SELECT "fullVisitorId" , count("fullVisitorId") AS amountOfVisits FROM all_sessions
GROUP BY "fullVisitorId" 
HAVING count("fullVisitorId") > 1 
ORDER BY count("fullVisitorId") DESC
LIMIT 10)

SELECT DISTINCT a.*, "country" FROM all_sessions as a_s
JOIN cte AS a ON a."fullVisitorId" = a_s."fullVisitorId"
ORDER BY amountOfVisits DESC

Answer: The top 10 users that visit this site are all based in the US with the person
who has visited the site the most visiting it ten times. 


Question 2: Compare the total transaction revenue between the top 10 people who visit
the site and the actual top 10 total transaction revenues

SQL Queries:

WITH cte as (SELECT "fullVisitorId" , count("fullVisitorId") AS amountOfVisits FROM all_sessions
GROUP BY "fullVisitorId" 
HAVING count("fullVisitorId") > 1 
ORDER BY count("fullVisitorId") DESC
LIMIT 10)

SELECT DISTINCT a.*, "totalTransactionRevenue" FROM all_sessions as a_s
JOIN cte AS a ON a."fullVisitorId" = a_s."fullVisitorId"
ORDER BY amountOfVisits DESC

SELECT DISTINCT "fullVisitorId", "totalTransactionRevenue" FROM all_sessions
ORDER BY "totalTransactionRevenue" DESC
LIMIT 10

Answer: When looking at the top 10 people who visit the site all of them except one
have not even had a transaction on the site. On the other hand the visitor that has
had transactions is also the person who has spent the most amount of money on the site
by a large margin. It doesn't seem that there is a correlation between page visits
and transaction revenue as people with lower page visits have a transaction amounts
and vice versa.



Question 3: 

SQL Queries:

WITH cte as (SELECT "fullVisitorId" , count("fullVisitorId") AS amountOfVisits FROM all_sessions
GROUP BY "fullVisitorId" 
HAVING count("fullVisitorId") > 1 
ORDER BY count("fullVisitorId") DESC
)

SELECT amountOfVisits, AVG("totalTransactionRevenue") AS AverageRevenuePerPerson FROM cte
JOIN all_sessions AS a_s ON a_s."fullVisitorId" = cte."fullVisitorId"
GROUP BY amountOfVisits
ORDER BY amountOfVisits

Answer: 

amountOfVisits, averageRevenuePerPerson
2	3549.549019607843
3	32.671875
4	0
6	3280752
7	0
8	0
10	0




