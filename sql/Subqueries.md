# Subquerying with semi joins and anti joins

In this lesson, explore advanced SQL techniques with nested queries and joins. You'll learn about semi and anti joins, which filter data without adding new columns.

Understand how INNER JOIN adds columns to the left table.
Learn how aliasing resolves duplicate column names.
Discover semi joins to filter records based on conditions in another table.
Use anti joins to exclude records without matches in another table.
Here's a practical example using a subquery:

    SELECT country, president 
    FROM countries 
    WHERE country IN (
    SELECT country 
    FROM states 
    WHERE indep_year < 1800
    );

This query filters countries based on their independence year. Let's dive into exercises!

## Semi join

Great job getting acquainted with semi joins and anti joins! You are now going to practice using semi joins.

Let's say you are interested in identifying languages spoken in the Middle East. The languages table contains information about languages and countries, but it does not tell you what region the countries belong to. You can build up a semi join by filtering the countries table by a particular region, and then using this to further filter the languages table.

You'll build up your semi join as you did in the video exercise, block by block, starting with a selection of countries from the countries table, and then leveraging a WHERE clause to filter the languages table by this selection.

Select country code as a single field from the countries table, filtering for countries in the 'Middle East' region.

    -- Select country code for countries in the Middle East
    SELECT code 
    FROM countries
    WHERE region = 'Middle East';

Select country code as a single field from the countries table, filtering for countries in the 'Middle East' region.

    -- Select unique language names
    SELECT DISTINCT name
    FROM languages
    -- Order by the name of the language
    ORDER BY name;

    SELECT DISTINCT name
    FROM languages
    -- Add syntax to use bracketed subquery below as a filter
    WHERE code IN
        (SELECT code
        FROM countries
        WHERE region = 'Middle East')
    ORDER BY name;

## Diagnosing problems using anti join

Nice work on semi joins! The anti join is a related and powerful joining tool. It can be particularly useful for identifying whether an incorrect number of records appears in a join.

Say you are interested in identifying currencies of Oceanian countries. You have written the following INNER JOIN, which returns 15 records. Now, you want to ensure that all Oceanian countries from the countries table are included in this result. You'll do this in the first step.

    SELECT c1.code, name, basic_unit AS currency
    FROM countries AS c1
    INNER JOIN currencies AS c2
    ON c1.code = c2.code
    WHERE c1.continent = 'Oceania';

If there are any Oceanian countries excluded in this INNER JOIN, you want to return the names of these countries. You'll write an anti join to this in the second step!

Begin by writing a query to return the code and name (in order, not aliased) for all countries in the continent of Oceania from the countries table.
Observe the number of records returned and compare this with the provided INNER JOIN, which returns 15 records.

    -- Select code and name of countries from Oceania
    SELECT code, name 
    FROM countries
    WHERE continent = 'Oceania';

Now, build on your query to complete your anti join, by adding an additional filter to return every country code that is not included in the currencies table.

