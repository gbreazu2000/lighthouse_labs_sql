Question 1: Is there a correlation between the amount of time that a customer spends on the site and the total orders made by the same customer?

SQL Queries:


SELECT
    CORR(timeonsite, total_ordered) AS correlation_coefficient,
    AVG(timeonsite) AS avg_timeonsite,
    AVG(total_ordered) AS avg_total_ordered,
    STDDEV(timeonsite) AS stddev_timeonsite,
    STDDEV(total_ordered) AS stddev_total_ordered
FROM
    all_sessions
JOIN
	sales_report USING(productsku)


Answer: No, there is no correlation between the time spent on the site and the total order number.



Question 2: Is there a correlation between the time it takes to restock an item and the popularity of that item in terms of orders orders?

SQL Queries:


SELECT
    AVG(restockingleadtime) AS avg_restockingleadtime,
    AVG(total_ordered) AS avg_totalordered,
	CORR(restockingleadtime, total_ordered) as correlation_coefficient
FROM
    all_sessions
JOIN
	sales_report USING(productsku)


Answer: Using this function, we can determine that there is no correlation between the restockingleadtime and total_ordered.



Question 3: Is this commence business seasonal or do they generate revenue year round?


SQL Queries:


SELECT EXTRACT(MONTH FROM date) as month_of_the_year,
	   SUM(totaltransactionrevenue) as total_revenue
FROM all_sessions
GROUP BY EXTRACT(MONTH FROM date)
ORDER BY EXTRACT(MONTH FROM date) ASC


Answer: There seems to be a drop in revenue around fall time, however it seems to have pretty consistent sales throughout the year.




