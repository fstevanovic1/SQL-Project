What are your risk areas? Identify and describe them.

Risk areas include making sure that all numerical values are reasonable. Also need to 
check if aggregation functions correctly sum over the numbers to output correct
revenue, transactionRevenue and totalTransactionRevenue values.

QA Process:
Describe your QA process and include the SQL queries used to execute it.

For making sure all the numerical values for unit_price, revenue, transactionRevenue and
totalTransactionRevenue are reasonable one way is to check if any of the values are still
over one million since they needed to be normalized.

SELECT "fullVisitorId", "unit_price" FROM analytics
WHERE "unit_price" > 1000000

We do the above code swapping out unit_price and analytics for revenue, transactionRevenue and
totalTransactionRevenue and the corresponding tables to make sure there are no outliers
that do not make sense. We actually some values in totalTransactionRevenue that are larger
than one million so we need to go into analytics to check if this makes sense. Since it is 
small set of values we can manually check via the fullVisitorId and these values do make
sense.

Next we need to check if the code has properly aggregated our columns. One way to check
this is actually to compare the sum of every revenue for one person to the sum of all the 
transactionRevenue's for one person to their totalTransactionRevenue. These values should 
equal eachother so if they aren't equal then that is a problem.

SELECT DISTINCT "fullVisitorId", "totalTransactionRevenue", SUM("transactionRevenue"), SUM("revenue") FROM all_sessions
JOIN analytics USING ("fullVisitorId")
GROUP BY all_sessions."fullVisitorId", "totalTransactionRevenue"

Using this we can see if there are discrepancies between the values. In this case there are
because there are transactions that occur in all_sessions and do not in analytics and vice versa.
This means the data should be used to infer rows between the two tables.

