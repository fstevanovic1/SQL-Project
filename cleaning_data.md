What issues will you address by cleaning the data?

The issues I plan to solve in cleaning the data is remove inconsistencies and NULL values
for the columns totalTransactionRevenue, transactionRevenue within all_sessions and 
revenue, units_sold and unit_price in analytics. 

First I would will in all values that are NULL for these columns with zeroes. The assumption
here is that since there are some values filled in that the NULL boxes are just the absence
of a number and therefore a zero would be equivalent.

Next I noticed that nearly all the prices are very large numbers. If I go look at the actual
products that are being sold I see that they are mostly household items so they should not
be this expensive. Therefore I will normalize all the numbers by dividing them by 1000000.

After that I want to fill in the revenue, transactionRevenue and totalTransactionRevenue
columns. I will do that by multiplying unit_price*unit_sold to get the revenue. Then I will
sum over the revenue within a transaction to get the transactionRevenue. Then I can sum
over all the transactionRevenue's(or over all the revenue's) for a visitor to get the
totalTransactionRevenue.


Queries:
Below, provide the SQL queries you used to clean your data.

#Use the following two queries with totalTransactionRevenue, transactionRevenue
#in all_sessions and revenue, units_sold and unit_price in analytics

UPDATE analytics
SET "units_sold" = 0
WHERE "units_sold" IS NULL

UPDATE analytics 
SET "unit_price" = "unit_price"/1000000

#Update revenue column

UPDATE analytics
SET "revenue" = "unit_price"*"units_sold"
FROM analytics AS a
WHERE a."fullVisitorId" = all_sessions."fullVisitorId" 

#Update transactionRevenue column

with cte as(
SELECT a."visitId", a."fullVisitorId", SUM("revenue") as sumRevenue from all_sessions as a_s
JOIN analytics as a ON a_s."fullVisitorId" = a."fullVisitorId"
GROUP BY a."fullVisitorId", a."visitId"
ORDER BY a."fullVisitorId", a."visitId")

UPDATE all_sessions
SET "transactionRevenue" = cte.sumRevenue
FROM cte
WHERE cte."visitId" = all_sessions."visitId" AND cte."fullVisitorId" = all_sessions."fullVisitorId" 

#Update totalTransactionRevenue column

with cte as(
	SELECT a."visitId", a."fullVisitorId", SUM("revenue") as transactionRevenue 
	FROM all_sessions as a_s
	JOIN analytics as a ON a_s."fullVisitorId" = a."fullVisitorId"
	GROUP BY a."fullVisitorId", a."visitId"
	ORDER BY a."fullVisitorId", a."visitId"
),

cte2 as(
	SELECT "fullVisitorId", SUM(transactionRevenue) AS totalTransactionRevenue 
	FROM cte 
	GROUP BY "fullVisitorId"
)

UPDATE all_sessions
SET "totalTransactionRevenue" = totalTransactionRevenue
FROM cte2
WHERE cte2."fullVisitorId" = all_sessions."fullVisitorId" 



