What are your risk areas? Identify and describe them.


I would say the risk areas in this data is the fullvisitorid, visitid and city data. There seems to be a lot of duplicates in the dataset, so we need to make sure that primary keys are unique values.


QA Process:
Describe your QA process and include the SQL queries used to execute it.


QUALITY ASSURANCE QUERIES:

PRIMARY KEY QUALITY ASSURANCE:

SELECT productsku, COUNT(*)
FROM sales_report
GROUP BY productsku
HAVING COUNT(*) > 1;

SELECT productsku, COUNT(*)
FROM sales_by_sku
GROUP BY productsku
HAVING COUNT(*) > 1;

QA ENSURING NO NULL VALUES:

SELECT fullvisitorid
FROM all_sessions
WHERE fullvisitorid is NULL

QA ensuring no duplications in city-country combinations:

SELECT city, COUNT(country)
FROM all_sessions
GROUP BY city
HAVING COUNT(*) > 1;
