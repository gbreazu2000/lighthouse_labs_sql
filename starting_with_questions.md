Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries:


City:

SELECT SUM(totaltransactionrevenue) as sum_orders_by_city, city
FROM all_sessions
GROUP BY city
HAVING SUM(totaltransactionrevenue) > 1
ORDER BY sum_orders_by_city DESC
LIMIT 5

Country:

SELECT SUM(totaltransactionrevenue) as sum_orders_by_city, city
FROM all_sessions
GROUP BY city
HAVING SUM(totaltransactionrevenue) > 1
ORDER BY sum_orders_by_city DESC
LIMIT 5


Answer: Looking at the data, we can determine the largest amount of the revenue comes the United States along with Israel and Australia slightly trailing in numbers. In the United States, an unknown city holds the highest amount of revenue generated, with San Francisco and SunnyVale generating around a fifth of the revenue compared to the unknown city. 



**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:


SELECT DISTINCT(city),
	   AVG(total_ordered) OVER (PARTITION BY city) AS avg_per_city
FROM sales_report
JOIN all_sessions USING(productsku)
GROUP BY city, total_ordered
ORDER BY avg_per_city DESC

SELECT DISTINCT(country),
	   AVG(total_ordered) OVER (PARTITION BY country) AS avg_per_country
FROM sales_report
JOIN all_sessions USING(productsku)
GROUP BY country, total_ordered
ORDER BY avg_per_country DESC

Answer: For the countries, Saudi Arabia, Kuwait and Ethiopia seem have the highest average of orders per country. For the cities, Brno, Riyadh and Rexburg have the highest average of orders per city.



**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:


SELECT
    country,
    city,
    v2productcategory,
    SUM(total_ordered) OVER (PARTITION BY v2productcategory) AS total_ordered_for_category,
    total_ordered
FROM
    sales_report
JOIN
    all_sessions USING (productsku)
WHERE
	country  != '(not set)'
ORDER BY
    country, city, v2productcategory, total_ordered_for_category DESC;


Answer: Judging from this table and using the count function, we can observe that a very common product category is Youtube accessories. This can be visualized by looking at the highest sum of products.





**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:


WITH ranked_products AS (
    SELECT
        country,
        city,
        productsku,
		name,
		productprice,
		time,
		pagetitle,
        ROW_NUMBER() OVER (PARTITION BY country, city ORDER BY SUM(total_ordered) DESC) AS rank
    FROM
        sales_report
    JOIN
        all_sessions USING (productsku)
    GROUP BY
        country, city, productsku, name, productprice, time, page title
)

SELECT country,
	   city,
	   productsku,
	   name,
	   productprice,
	   time,
	   pagetitle
FROM ranked_products
WHERE rank = 1
GROUP BY country, city, productsku, name, productprice, time, pagetitle


Answer: As we can notice from looking at the table, there is a variety of different items that are ranked #1 in orders for each city and/or country. We can notice a small pattern looking at the page title column, showing that a large majority of orders were purchased based on the Youtube brand. Therefore, we can assume that there is a higher demand for youtube merchandise than the others.



**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:


SELECT
    country,
    SUM(CASE WHEN EXTRACT(YEAR FROM date) = 2016 THEN totaltransactionrevenue ELSE 0 END) AS revenue_2016,
    SUM(CASE WHEN EXTRACT(YEAR FROM date) = 2017 THEN totaltransactionrevenue ELSE 0 END) AS revenue_2017,
    SUM(CASE WHEN EXTRACT(YEAR FROM date) = 2017 THEN totaltransactionrevenue ELSE 0 END) -
    SUM(CASE WHEN EXTRACT(YEAR FROM date) = 2016 THEN totaltransactionrevenue ELSE 0 END) AS revenue_increase
FROM
    all_sessions
WHERE
    EXTRACT(YEAR FROM date) IN (2016, 2017)
GROUP BY
    country
HAVING
	SUM(CASE WHEN EXTRACT(YEAR FROM date) = 2017 THEN totaltransactionrevenue ELSE 0 END) -
    SUM(CASE WHEN EXTRACT(YEAR FROM date) = 2016 THEN totaltransactionrevenue ELSE 0 END) > 1
ORDER BY
    country;


Answer: I used the sum aggregate function to determine the increase of revenue in the span of a year with respect to different countries. In the results of this query, we can determine that more countries have bought products from this commence store in 2017 than 2016.







