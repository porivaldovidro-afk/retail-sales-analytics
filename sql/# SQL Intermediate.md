# SQL Intermediate

This lesson dives into using SQL to transform raw data into insights. You'll focus on querying databases to extract meaningful information.

    Learn to execute queries using keywords like COUNT and DISTINCT
    Understand common SQL errors and style guidelines
    Explore filtering, sorting, and grouping techniques
    Work with PostgreSQL and a films database

Here's a quick example:

SELECT COUNT(DISTINCT birthdate) AS unique_birthdates FROM people;

This query counts unique birthdates in the people table, highlighting the difference between total and distinct counts. Let's get started with counting!

## Learning to COUNT()

You saw how to use COUNT() in the video. Do you remember what it returns?

Here is a query counting film_id. Select the answer below that correctly describes what the query will return.

SELECT COUNT(film_id) AS count_film_id
FROM reviews;

Run the query in the console to test your theory!

## Practice with COUNT()

As you've seen, COUNT(*) tells you how many records are in a table. However, if you want to count the number of non-missing values in a particular field, you can call COUNT() on just that field.

Let's get some practice with COUNT()! You can look at the data in the tables throughout these exercises by clicking on the table name in the console.

-- Count the number of records in the people table
SELECT COUNT(*) AS count_records FROM people;

SELECT COUNT(language) AS count_languages, COUNT(country) AS count_countries FROM films;

-- Count the distinct countries from the films table
SELECT COUNT(DISTINCT country) AS count_distinct_countries FROM films;


In this lesson, you'll dive into understanding how SQL code is processed and learn essential debugging skills. Here's what you'll cover:

    SQL processing order: FROM, SELECT, and LIMIT
    Importance of knowing processing order for debugging and aliasing
    Common SQL errors: misspellings, incorrect punctuation, and capitalization
    How to interpret error messages for effective debugging

Here's a simple SQL example to illustrate:

SELECT name 
FROM people 
LIMIT 10;

Remember, debugging is a key skill, and practice will help you master it. Let's get started!

In this lesson, you'll learn about the importance of SQL formatting and style standards for writing clear, readable code. Key points include:

    SQL doesn't require strict formatting, but following style guides is beneficial.
    Capitalization and new lines improve readability.
    Using semicolons at the end of queries is a best practice.
    Avoid spaces in field names to prevent complications.

Here's a sample SQL query with proper formatting:

SELECT 
    title, 
    release_year, 
    country 
FROM 
    films 
LIMIT 3;

Following these guidelines ensures easier collaboration and understanding of SQL code.


## Formatting

Readable code is highly valued in the coding community and professional settings. Without proper formatting, code and results can be difficult to interpret. You'll often be working with other people that need to understand your code or be able to explain your results, so having a solid formatting habit is essential.

In this exercise, you'll correct poorly written code to better adhere to SQL style standards.