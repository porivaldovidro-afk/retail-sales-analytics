# Outer Joins, Cross Joins and Self Joins

This is a guide to understanding different types of SQL joins.

In this lesson, you'll explore outer joins, cross joins, and self joins, starting with a focus on LEFT and RIGHT JOINs.

LEFT JOIN: Retrieves all records from the left table and matching records from the right table. Non-matching right table entries return null.
RIGHT JOIN: Retrieves all records from the right table and matching records from the left table. Non-matching left table entries return null.
Here's a quick look at the LEFT JOIN syntax:

  SELECT left_table.id, left_table.value, right_table.value
  FROM left_table
  LEFT JOIN right_table ON left_table.id = right_table.id;

Understanding these joins will enhance your SQL querying skills. Let's dive into exercises and solidify your understanding!

## This is a LEFT JOIN, right?

Nice work getting to grips with the structure of joins! In this exercise, you'll explore the differences between INNER JOIN and LEFT JOIN. This will help you decide which type of join to use.

As before, you will be using the cities and countries tables.

You'll begin with an INNER JOIN with the cities table (left) and countries table (right). This helps if you are interested only in records where a country is present in both tables.

You'll then change to a LEFT JOIN. This helps if you're interested in returning all countries in the cities table, whether or not they have a match in the countries table.

Perform an inner join with cities AS c1 on the left and countries as c2 on the right.
Use code as the field to merge your tables on.

  SELECT 
      c1.name AS city,
      code,
      c2.name AS country,
      region,
      city_proper_pop
  FROM cities AS c1
  -- Perform an inner join with cities as c1 and countries as c2 on country code
  INNER JOIN countries AS c2
  ON c2.code = c1.country_code
  ORDER BY c2.code DESC;

Change the code to perform a LEFT JOIN instead of an INNER JOIN.
After executing this query, have a look at how many records the query result contains.

  SELECT 
    c1.name AS city, 
      code, 
      c2.name AS country,
      region, 
      city_proper_pop
  FROM cities AS c1
  -- Join right table (with alias)
  LEFT JOIN countries AS c2
  ON c1.country_code = c2.code
  ORDER BY code DESC;

## Building on your LEFT JOIN

You'll now revisit the use of the AVG() function introduced in a previous course.

Being able to build more than one SQL function into your query will enable you to write compact, supercharged queries.

You will use AVG() in combination with a LEFT JOIN to determine the average gross domestic product (GDP) per capita by region in 2010.

Complete the LEFT JOIN with the countries table on the left and the economies table on the right on the code field.
Filter the records from the year 2010.

  SELECT name, region, gdp_percapita
  FROM countries AS c
  LEFT JOIN economies AS e
  -- Match on code fields
  USING (code)
  -- Filter for the year 2010
  WHERE year = 2010;

To calculate per capita GDP per region, begin by grouping by region.
After your GROUP BY, choose region in your SELECT statement, followed by average GDP per capita using the AVG() function, with AS avg_gdp as your alias.

  -- Select region, and average gdp_percapita as avg_gdp
  SELECT region, AVG(gdp_percapita) AS avg_gdp
  FROM countries AS c
  LEFT JOIN economies AS e
  USING(code)
  WHERE year = 2010
  -- Group by region
  GROUP BY region;

Order the result set by the average GDP per capita from highest to lowest.
Return only the first 10 records in your result.

  SELECT region, AVG(gdp_percapita) AS avg_gdp
  FROM countries AS c
  LEFT JOIN economies AS e
  USING(code)
  WHERE year = 2010
  GROUP BY region
  -- Order by descending avg_gdp
  ORDER BY avg_gdp DESC
  -- Return only first 10 records
  LIMIT 10;

## Is this RIGHT?

You learned that right joins are not used as commonly as left joins. A key reason for this is that right joins can always be re-written as left joins, and because joins are typically typed from left to right, joining from the left feels more intuitive when constructing queries.

It can be tricky to wrap one's head around when left and right joins return equivalent results. You'll explore this in this exercise!

# FULL JOINs

In this lesson, explore the concept of FULL JOIN, the third type of outer join. Understand how FULL JOIN differs from LEFT JOIN, RIGHT JOIN, and INNER JOIN. Key points include:

  * FULL JOIN combines LEFT and RIGHT JOIN, returning all ids from both tables.
  * Missing matches are represented as nulls in the result.
  * Syntax: Use 'FULL JOIN' or 'FULL OUTER JOIN' in SQL.
  Here's an example code snippet:

  SELECT country, prime_minister, president
  FROM prime_ministers AS p1
  FULL JOIN presidents AS p2
  ON p1.country = p2.country
  LIMIT 10;

This query lists countries with their prime ministers and presidents, even if one is missing.

## Comparing joins

In this exercise, you'll examine how results can differ when performing a full join compared to a left join and inner join by joining the countries and currencies tables. You'll be focusing on the North American region and records where the name of the country is missing.

You'll begin with a full join with countries on the left and currencies on the right. Recall the workings of a full join with the diagram below!

You'll then complete a similar left join and conclude with an inner join, observing the results you see along the way.

Perform a full join with countries (left) and currencies (right).
Filter for the North America region or NULL country names.

  SELECT c.name AS country, c.region, cu.name AS currency
  FROM countries AS c
  FULL JOIN currencies AS cu
  ON c.code = cu.country_code
  WHERE c.region = 'North America' OR c.name IS NULL;

Repeat the same query as before, turning your full join into a left join with the currencies table.
Have a look at what has changed in the output by comparing it to the full join result.

