What issues will you address by cleaning the data?

The CSV data that was imported contains a lot of data values errors and duplications among other things, therefore my objective was to delete as many duplicates as possible without affecting the integrity of the database. The values surrounding price also required intervention since many values were in the millions which is not a proper representation.



Queries:
Below, provide the SQL queries you used to clean your data.

WITH all_sessions_cte AS
    (SELECT *, ROW_NUMBER() OVER (PARTITION BY fullvisitorid, time, date, visitid, productsku ORDER BY fullvisitorid) AS rownumber
     FROM all_sessions
    )
SELECT * 
FROM all_sessions_cte
WHERE rownumber > 1

***DELETING DUPLICATES USING THE ROW_FUNCTION

WITH analytics_cte AS (
    SELECT
        *,
        ROW_NUMBER() OVER (PARTITION BY fullvisitorid, visitid, date, unit_price ORDER BY fullvisitorid) AS rownumber
    FROM
        analytics
)
DELETE FROM analytics
WHERE (fullvisitorid, visitid, date, unit_price) IN (
    SELECT fullvisitorid, visitid, date, unit_price
    FROM analytics_cte
    WHERE rownumber > 1
);

--Changing the values surrounding the price and revenue since it is in the wrong format and needs to be divided by a million.

UPDATE all_sessions
SET fullvisitorid = FLOOR(fullvisitorid / 100000000000);

UPDATE analytics
SET fullvisitorid = FLOOR(fullvisitorid / 100000000000);

UPDATE all_sessions
SET productprice = ROUND(productprice / 1000000 :: numeric , 2)

UPDATE all_sessions
SET totaltransactionrevenue = ROUND(totaltransactionrevenue / 1000000 :: numeric , 2)

UPDATE all_sessions
SET transactionrevenue = ROUND(transactionrevenue / 1000000 :: numeric , 2)

UPDATE all_sessions
SET productrevenue = ROUND(productrevenue / 1000000 :: numeric , 2)

UPDATE analytics
SET unit_price = ROUND(unit_price / 1000000 :: numeric , 2)

UPDATE sales_report
SET ratio = ROUND(ratio, 3)

