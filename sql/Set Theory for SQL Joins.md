# Set Theory for SQL Joins

In this lesson, you'll dive into set operations in SQL, focusing on UNION and UNION ALL. These operations allow you to combine data from two tables without needing a join field.

UNION: Combines records from both tables, removing duplicates.
UNION ALL: Combines records, including duplicates.
Key points include understanding the syntax and differences between these operations and joins. Remember, the number of columns and their data types must match.

Here's a quick example:

    SELECT leader FROM monarchs
    UNION
    SELECT leader FROM prime_ministers
    LIMIT 10;

Explore how these operations can reveal unique insights in your data.

## UNION vs. UNION ALL

Nice work learning all about UNION and UNION ALL!

Two tables, languages and currencies, are provided. Run the queries provided in the console and select the correct answer for the multiple-choice questions in this exercise.


## Comparing global economies

Are you ready to perform your first set operation?

In this exercise, you have two tables, economies2015 and economies2019, available to you under the tabs in the console. You'll perform a set operation to stack all records in these two tables on top of each other, excluding duplicates.

When drafting queries containing set operations, it is often helpful to write the queries on either side of the operation first, and then call the set operator. The instructions are ordered accordingly.

Begin your query by selecting all fields from economies2015.
Create a second query that selects all fields from economies2019.
Perform a set operation to combine the two queries you just created, ensuring you do not return duplicates.

    -- Select all fields from economies2015
    SELECT * 
    FROM economies2015
    -- Set operation
    UNION
    -- Select all fields from economies2019
    SELECT *
    FROM economies2019
    ORDER BY code, year;

## Comparing two set operations

You learned in the video exercise that UNION ALL returns duplicates, whereas UNION does not. In this exercise, you will dive deeper into this, looking at cases for when UNION is appropriate compared to UNION ALL.

You will be looking at combinations of country code and year from the economies and populations tables.

Perform an appropriate set operation that determines all pairs of country code and year (in that order) from economies and populations, excluding duplicates.
Order by country code and year.

    -- Query that determines all pairs of code and year from economies and populations, without duplicates
    SELECT code, year
    FROM economies
    UNION
    SELECT country_code, year
    FROM populations
    ORDER BY code, year;


Amend the query to return all combinations (including duplicates) of country code and year in the economies or the populations tables.

    SELECT code, year
    FROM economies
    -- Set theory clause
    UNION ALL
    SELECT country_code, year
    FROM populations
    ORDER BY code, year;

# At the Intersect

In this lesson, you'll dive into the INTERSECT set operation. INTERSECT is used to find common records between two tables. Here’s what you’ll learn:

INTERSECT returns only records present in both tables.
Syntax is similar to UNION, requiring identical columns in both tables.
Unlike INNER JOIN, INTERSECT does not return duplicate records.
Example Code:

    SELECT country FROM prime_ministers
    INTERSECT
    SELECT country FROM presidents;

This query finds countries with both a prime minister and a president. Remember, INTERSECT requires all fields to match for records to be returned.

## INTERSECT

Well done getting through the material on INTERSECT!

Let's say you are interested in those countries that share names with cities. Use this task as an opportunity to show off your knowledge of set theory in SQL!

-- Return all cities with the same name as a country
    
    SELECT name
    FROM cities
    INTERSECT
    SELECT name 
    FROM countries;

# EXCEPT

In this lesson, you'll explore the EXCEPT operation in SQL, which helps identify records present in one table but not another. Key points include:

EXCEPT retains records from the left table not found in the right table.
Useful for scenarios like finding monarchs who are not also prime ministers.
Here's a code snippet to illustrate:

    SELECT monarch, country
    FROM monarchs
    EXCEPT
    SELECT prime_minister, country
    FROM prime_ministers;

This query returns monarchs who do not hold the prime minister title. Keep practicing to solidify your understanding of set operations!

## You've got it, EXCEPT...

Just as you were able to leverage INTERSECT to find the names of cities with the same names as countries, you can also do the reverse, using EXCEPT.

In this exercise, you will find the names of cities that do not have the same names as their countries.

Return all cities that do not have the same name as a country.

-- Return all cities that do not have the same name as a country
   
    SELECT name 
    FROM cities
    EXCEPT
    SELECT name
    FROM countries
    ORDER BY name;

