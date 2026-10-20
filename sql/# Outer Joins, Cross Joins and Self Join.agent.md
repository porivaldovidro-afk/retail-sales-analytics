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

  SELECT name AS country, code, region, basic_unit
  FROM countries
  -- Join to currencies
  LEFT JOIN currencies
  USING (code)
  WHERE region = 'North America' 
    OR name IS NULL
  ORDER BY region;

Repeat the same query again, this time performing an inner join of countries with currencies.
Have a look at what has changed in the output by comparing it to the full join and left join results!

  SELECT name AS country, code, region, basic_unit
  FROM countries
  -- Join to currencies
  INNER JOIN currencies
  USING (code)
  WHERE region = 'North America' 
    OR name IS NULL
  ORDER BY region;

## Chaining FULL JOINs

As you have seen in the previous chapter on INNER JOIN, it is possible to chain joins in SQL, such as when looking to connect data from more than two tables.

Suppose you are doing some research on Melanesia and Micronesia, and are interested in pulling information about languages and currencies into the data we see for these regions in the countries table. Since languages and currencies exist in separate tables, this will require two consecutive full joins involving the countries, languages and currencies tables.

  SELECT 
    c1.name AS country, 
      region, 
      l.name AS language,
    basic_unit, 
      frac_unit
  FROM countries as c1 
  -- Full join with languages (alias as l)
  FULL JOIN  languages AS l 
  USING (code)
  -- Full join with currencies (alias as c2)
  FULL JOIN currencies AS c2
  USING (code)
  WHERE region LIKE 'M%esia';

# Crossing into cross join

In this lesson, you'll explore CROSS JOINs, a unique type of SQL join that creates all possible combinations between two tables.

Understand how CROSS JOIN differs from INNER and OUTER JOINs
Learn the minimal syntax for CROSS JOIN, without ON or USING clauses
Apply CROSS JOIN to pair Asian prime ministers with South American presidents for meetings
Here's a quick look at the syntax:

  SELECT *
  FROM table1
  CROSS JOIN table2;

This query will help you list all potential meetings between leaders from different continents. Now, let's tackle some exercises to solidify your understanding!

## Histories and languages

Well done getting to know all about CROSS JOIN! As you have learned, CROSS JOIN can be incredibly helpful when asking questions that involve looking at all possible combinations or pairings between two sets of data.

Imagine you are a researcher interested in the languages spoken in two countries: Pakistan and India. You are interested in asking:

What are the languages presently spoken in the two countries?
Given the shared history between the two countries, what languages could potentially have been spoken in either country over the course of their history?
In this exercise, we will explore how INNER JOIN and CROSS JOIN can help us answer these two questions, respectively.

Complete the code to perform an INNER JOIN of countries AS c with languages AS l using the code field to obtain the languages currently spoken in the two countries.

  SELECT c.name AS country, l.name AS language
  -- Inner join countries as c with languages as l on code
  FROM countries AS c
  INNER JOIN languages AS l
  USING (code)
  WHERE c.code IN ('PAK','IND')
    AND l.code in ('PAK','IND');

Change your INNER JOIN to a different kind of join to look at possible combinations of languages that could have been spoken in the two countries given their history.
Observe the differences in output for both joins.

  SELECT c.name AS country, l.name AS language
  FROM countries AS c        
  -- Perform a cross join to languages (alias as l)
  CROSS JOIN languages AS l
  WHERE c.code in ('PAK','IND')
    AND l.code in ('PAK','IND');

## Choosing your join

Now that you're fully equipped to use joins, try a challenge problem to test your knowledge!

You will determine the names of the five countries and their respective regions with the lowest life expectancy for the year 2010. Use your knowledge about joins, filtering, sorting and limiting to create this list!

Complete the join of countries AS c with populations as p.
Filter on the year 2010.
Sort your results by life expectancy in ascending order.
Limit the result to five countries.

  SELECT 
    c.name AS country,
      region,
      life_expectancy AS life_exp
  FROM countries AS c
  -- Join to populations (alias as p) using an appropriate join
  INNER JOIN populations AS p 
  ON c.code = p.country_code
  -- Filter for only results in the year 2010
  WHERE year = 2010
  -- Sort by life_exp
  ORDER BY life_exp
  -- Limit to five records
  LIMIT 5;

# Self joins

In this lesson, you'll explore self joins, a unique SQL technique where a table is joined with itself. This approach is useful for comparing values within the same table. Key points include:

Self Joins: Used to compare and pair values within the same table.
Aliasing: Essential for distinguishing table instances in self joins.
INNER JOIN Example: Pair countries on the same continent using the prime_ministers table.
Excluding Self-Pairs: Use the AND clause with != to avoid pairing a country with itself.
Here's a sample code snippet:

SELECT p1.country, p2.country
FROM prime_ministers p1
INNER JOIN prime_ministers p2
ON p1.continent = p2.continent
AND p1.country != p2.country
LIMIT 10;

Practice with exercises and tackle a joining challenge next!


## Comparing a country to itself

Self joins are very useful for comparing data from one part of a table with another part of the same table. Suppose you are interested in finding out how much the populations for each country changed from 2010 to 2015. You can visualize this change by performing a self join.

In this exercise, you'll work to answer this question by joining the populations table with itself. Recall that, with self joins, tables must be aliased. Use this as an opportunity to practice your aliasing!

Since you'll be joining the populations table to itself, you can alias populations first as p1 and again as p2. This is good practice whenever you are aliasing tables with the same first letter.

Perform an inner join of populations with itself ON country_code, aliased p1 and p2 respectively.
Select the country_code from p1 and the size field from both p1 and p2, aliasing p1.size as size2010 and p2.size as size2015 (in that order).

  -- Select aliased fields from populations as p1
  SELECT p1.country_code, p1.size AS size2010, p2.size AS size2015
  -- Join populations as p1 to itself, alias as p2, on country code
  FROM populations AS p1
  INNER JOIN  populations AS p2
  ON p1.country_code = p2.country_code;

  SELECT 
    p1.country_code, 
      p1.size AS size2010, 
      p2.size AS size2015
  FROM populations AS p1
  INNER JOIN populations AS p2
  ON p1.country_code = p2.country_code
  -- Filter such that p1.year is always five years before p2.year
      WHERE p1.year = 2010 AND p2.year = 2015;

