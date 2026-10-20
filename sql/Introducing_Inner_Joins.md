# Joing Data in SQL PART 1

Dive into the world of SQL joins, a vital skill for combining data from different tables. This lesson focuses on INNER JOIN, a common join type, using PostgreSQL. Key points include:

Understanding keys: unique identifiers for records in tables.
INNER JOIN: Matches records with the same key values in both tables.
Syntax: Start with the join, list tables, specify matching fields with ON, and select desired fields.
Use table-dot-column_name format to avoid errors.
Simplify queries with aliases and the USING command.
Here's a sample INNER JOIN query:

  SELECT p1.country, p1.president, p2.prime_minister
  FROM presidents AS p1
  INNER JOIN prime_ministers AS p2
  USING (country);

Now, let's practice joining tables!

## Your first join

Throughout this course, you'll be working with the countries database, which contains information about the most populous cities in the world, along with country-level economic, population, and geographic data. The database also contains information on languages spoken in each country.

You can see the different tables in this database to get a sense of what they contain by clicking on the corresponding tabs. Familiarize yourself with the fields that seem to be shared across tables before you continue with the course.

In this exercise, you'll use the cities and countries tables to build your first inner join. You'll start off by selecting all columns in step 1, performing your join in step 2, and then refining your join to choose specific columns in step 3.

Begin by selecting all columns from the cities table, using the SQL shortcut that selects all.
-- Select all columns from cities

  SELECT *
  FROM cities

Perform an inner join with the cities table on the left and the countries table on the right; you do not need to alias tables here.
Join ON the country_code and code columns, making sure you identify them with the correct table.

  SELECT * 
  FROM cities
  -- Inner join to countries
  INNER JOIN countries
  -- Match on country codes
  ON cities.country_code = countries.code;

Complete the SELECT statement to keep three columns: the name of the city, the name of the country, and the region the country is located in (in this order).
Alias the name of the city AS city and the name of the country AS country.
-- Select name fields (with alias) and region 

  SELECT cities.name AS city, countries.name AS country, countries.region
  FROM cities
  INNER JOIN countries
  ON cities.country_code = countries.code;


## Joining with aliased tables

Table aliases are helpful in allowing you to reference them in other parts of your query, like the SELECT statement.

When you SELECT fields, a field can be ambiguous. For example, imagine two tables, apples and oranges, both containing a column called color. You need to use the syntax apples.color or oranges.color in your SELECT statement to point SQL to the correct table. Without this, you would get the following error:

  column reference "color" is ambiguous
You'll practice joining with aliased tables using data from both the countries and economies tables to examine the inflation rate in 2010 and 2015.

When writing joins, many SQL users prefer to write the SELECT statement after writing the join code, in case the SELECT statement requires using table aliases.

Start with your inner join in line 5; join the tables countries AS c (left) with economies (right), aliasing economies AS e.
Next, use code as your joining field in line 7; do not use the USING command here.
Lastly, select the following columns in order in line 2: code from the countries table (aliased as country_code), name, year, and inflation_rate.

  -- Select fields with aliases
  SELECT c.code AS country_code, c.name, e.year, e.inflation_rate
  FROM countries AS c
  -- Join to economies (alias e)
  INNER JOIN economies AS e
  -- Match on code field using table aliases
  ON e.code = c.code;

## USING in action

In the previous exercises, you performed your joins using the ON keyword. Recall that when both the field names being joined on are the same, you can take advantage of the USING clause.

You'll now explore the languages table from our database. Which languages are official languages, and which ones are unofficial?

You'll employ USING to simplify your query as you explore this question.

Use the country code field to complete the INNER JOIN with USING; do not change any alias names.

  SELECT c.name AS country, l.name AS language, official
  FROM countries AS c
  INNER JOIN languages AS l
  -- Match using the code column
  USING (code);

# PART 2

In this lesson, you'll explore the different types of relationships between tables in SQL. Understanding these relationships is crucial for effective database design and querying.

One-to-Many Relationship: Commonly seen in scenarios like artists and their songs or authors and their books. Each entity in one table can relate to multiple entities in another.

One-to-One Relationship: Unique pairings, such as a person and their fingerprint. Each entity in one table corresponds to exactly one entity in another.

Many-to-Many Relationship: Seen in cases like languages and countries, where multiple entities in one table can relate to multiple entities in another.

SELECT authors.name, books.title 
FROM authors 
JOIN books ON authors.author_id = books.author_id;

Now, apply these concepts in your exercises!

## Relationships in our database

Now that you know more about the different types of relationships that can exist between tables, it's time to examine a few relationships in the countries database!

To answer questions about table relationships, you can explore the tables displayed as tabs in your console.

### Inspecting a relationship

You've just identified that the countries table has a many-to-many relationship with the languages table. That is, many languages can be spoken in a country, and a language can be spoken in many countries.

But, what is the best way to query all the different languages spoken in a country? Or, all the countries that speak a certain language?

  -- Select country (aliased) from countries
  
  SELECT name AS country
  FROM countries

Now add an alias c for the countries table and perform an inner join with the languages table, l, on the right; join on code in line 8 with the USING keyword; include the language name, aliased as language.

  -- Select country and language names (aliased)
  SELECT c.name AS country, l.name AS language
  -- From countries (aliased)
  FROM countries AS c
  -- Join to languages (aliased)
  INNER JOIN languages AS l
  -- Use code as the joining field with the USING keyword
  USING (code);

Add a WHERE clause to find how many countries speak the language 'Bhojpuri'.

    -- Select country and language name (aliased)
  SELECT c.name AS country, l.name AS language
  -- From countries (aliased)
  FROM countries AS c
  -- Join to languages (aliased)
  INNER JOIN languages AS l
  -- Use code as the joining field with the USING keyword
  USING(code)
  -- Filter for the Bhojpuri language
  WHERE l.name = 'Bhojpuri';

Question
In how many countries is the 'Bhojpuri' language spoken? Select the correct answer from the following options.
The query you generated is provided below. Run this query to find the answer to the question.

##  PART 3

In this lesson, you'll explore advanced SQL joins, focusing on combining multiple joins in a single query. Here's what you'll learn:

How to chain multiple INNER JOINs to combine data from several tables.
Using specific fields for joins, like switching between left_table.id and right_table.id.
Applying these techniques to real-world examples, such as identifying countries with both a president and a prime minister.
Enhancing queries by joining on multiple fields using the AND keyword.
Here's an example of chaining joins:

  SELECT *
  FROM presidents
  INNER JOIN prime_ministers ON presidents.country = prime_ministers.country
  INNER JOIN prime_minister_terms ON prime_ministers.name = prime_minister_terms.prime_minister;

Now, practice these concepts with the exercises provided!

### Joining multiple tables

You've seen that the ability to combine multiple joins using a single query is a powerful feature of SQL.

Suppose you are interested in the relationship between fertility and unemployment rates. Your task in this exercise is to join tables to return the country name, year, fertility rate, and unemployment rate in a single result from the countries, populations and economies tables.

Do an inner join of countries AS c (left) with populations AS p (right), on code.
Select name and fertility_rate.

  -- Select relevant fields
  SELECT c.name, p.fertility_rate
  -- Inner join countries and populations, aliased, on code
  FROM countries AS c
  INNER JOIN populations AS p
  ON p.country_code = c.code;

Chain an inner join with the economies table AS e, on code.
Select year and unemployment_rate from the economies table.

  -- Select fields
  SELECT name, e.year, fertility_rate, unemployment_rate
  FROM countries AS c
  INNER JOIN populations AS p
  ON c.code = p.country_code
  -- Join to economies (as e)
  INNER JOIN economies AS e 
  -- Match on country code
  ON e.code = c.code;

### Checking multi-table joins

Have a look at the results for Albania from the previous query below. You can see that the 2015 fertility_rate has been paired with 2010 unemployment_rate, and vice versa.


name	year	fertility_rate	unemployment_rate
Albania	2015	1.663	17.1
Albania	2010	1.663	14
Albania	2015	1.793	17.1
Albania	2010	1.793	14
Instead of four records, the query should return two: one for each year. The last join was performed on c.code = e.code, without also joining on year. Your task in this exercise is to fix your query by explicitly stating that both the country code and year should match!

Instructions
100 XP
Modify your query so that you are joining to economies on year as well as code.

  SELECT name, e.year, fertility_rate, unemployment_rate
  FROM countries AS c
  INNER JOIN populations AS p
  ON c.code = p.country_code
  INNER JOIN economies AS e
  ON c.code = e.code
  -- Add an additional joining condition such that you are also joining on year
  AND e.year = p.year;

