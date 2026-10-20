# Intermediate SQl PART 4

In this lesson, you'll explore how to sort and group data to gain insights efficiently. Sorting helps organize data, making it easier to interpret. Here's what you'll learn:

Use ORDER BY to sort results in ascending (default) or descending order.
Utilize ASC and DESC keywords for clarity.
Filter data with WHERE before sorting to exclude null values.
Sort by multiple fields to refine order, using commas to separate fields.
Understand execution order: FROM, WHERE, SELECT, ORDER BY, LIMIT.
Example SQL query:

  SELECT title, budget
  FROM films
  WHERE budget IS NOT NULL
  ORDER BY budget DESC, title ASC;

Practice these skills to enhance your data analysis!

## Sorting single fields

Now that you understand how ORDER BY works, you'll put it into practice. In this exercise, you'll work on sorting single fields only. This can be helpful to extract quick insights such as the top-grossing or top-scoring film.

The following exercises will help you gain further insights into the film database.

Select the name of each person in the people table, sorted alphabetically.
-- Select name from people and sort alphabetically

  SELECT name 
  FROM people
  ORDER BY name;

Select the title and duration for every film, from longest duration to shortest.
-- Select the title and duration from longest to shortest film

  SELECT title, duration
  FROM films
  ORDER BY duration DESC;

## Sorting multiple fields

ORDER BY can also be used to sort on multiple fields. It will sort by the first field specified, then sort by the next, and so on. As an example, you may want to sort the people data by age and keep the names in alphabetical order.

Try using ORDER BY to sort multiple columns.

Select the release_year, duration, and title of films ordered by their release year and duration, in that order.
-- Select the release year, duration, and title sorted by release year and duration

  SELECT release_year, duration, title
  FROM films
  ORDER BY release_year ASC, duration ASC;


Select the certification, release_year, and title from films ordered first by certification (alphabetically) and second by release year, starting with the most recent year.
-- Select the certification, release year, and title sorted by certification and release year

  SELECT certification, release_year, title
  FROM films
  ORDER BY certification ASC, release_year DESC;

## Filtering and sorting (fields->campos)

In this lesson, you'll explore how to group data using SQL, focusing on the GROUP BY clause. This is key for summarizing data efficiently.

Learn to group data by fields like certification
Use aggregate functions for summary statistics
Understand how to handle non-grouped fields
Combine GROUP BY with ORDER BY for organized results
Here's a simple example:

SELECT certification, COUNT(title) AS title_count
FROM films
GROUP BY certification
ORDER BY title_count DESC;
This query groups films by certification, counts titles, and orders results by title count. Dive into exercises to practice these concepts!

### GROUP BY single fields

GROUP BY is a SQL keyword that allows you to group and summarize results with the additional use of aggregate functions. For example, films can be grouped by the certification and language before counting the film titles in each group. This allows you to see how many films had a particular certification and language grouping.

In the following steps, you'll summarize other groups of films to learn more about the films in your database.

Select the release_year and count of films released in each year aliased as film_count.
-- Find the release_year and film_count of each year

  SELECT release_year, COUNT(*) AS film_count
  FROM films
  GROUP BY release_year;

Select the release_year and average duration aliased as avg_duration of all films, grouped by release_year.
-- Find the release_year and average duration of films for each year

  SELECT release_year, AVG(duration) AS avg_duration
  FROM films
  GROUP BY release_year;

### GROUP BY multiple fields

GROUP BY becomes more powerful when used across multiple fields or combined with ORDER BY and LIMIT.

Perhaps you're interested in learning about budget changes throughout the years in individual countries. You'll use grouping in this exercise to look at the maximum budget for each country in each year there is data available.

Select the release_year, country, and the maximum budget aliased as max_budget for each year and each country; sort your results by release_year and country.
-- Find the release_year, country, and max_budget, then group and order by release_year and country

  SELECT release_year, country, MAX(budget) AS max_budget
  FROM films
  GROUP BY release_year, country
  ORDER BY release_year, country;

### Answering business questions

In the real world, every SQL query starts with a business question. Then it is up to you to decide how to write the query that answers the question. Let's try this out.

Using the films table: which release_year had the most language diversity?

Take your time to translate this question into code and test your queries in the console.

"Most language diversity" can be interpreted as COUNT(DISTINCT ___). Now over to you.

  SELECT release_year, COUNT(DISTINCT language) AS count_language
  FROM films
  GROUP BY release_year;

##  Combining Filtering and Grouping

In this lesson, you'll explore combining filtering with grouping in SQL. Key points include:

Filtering Aggregates: Learn why aggregate functions can't be filtered with WHERE and how HAVING is used instead.
Order of Execution: Understand the order: FROM, WHERE, GROUP BY, HAVING, SELECT, ORDER BY, LIMIT.
Practical Examples: Translate business questions into SQL queries using these concepts.
Here's a code snippet to illustrate:

  SELECT release_year
  FROM films
  GROUP BY release_year
  HAVING AVG(duration) > 120;

This query finds years with an average film duration over two hours. Practice these concepts to solidify your understanding.

### Filter with HAVING

Your final keyword is HAVING. It works similarly to WHERE in that it is a filtering clause, with the difference that HAVING filters grouped data.

Filtering grouped data can be especially handy when working with a large dataset. When working with thousands or even millions of rows, HAVING will allow you to filter for just the group of data you want, such as films over two hours in length!

Practice using HAVING to find out which countries (or country) have the most varied film certifications.

Select country from the films table, and get the distinct count of certification aliased as certification_count.
Group the results by country.
Filter the unique count of certifications to only results greater than 10.

-- Select the country and distinct count of certification as certification_count

  SELECT country, COUNT(DISTINCT certification) AS certification_count
  FROM films
  -- Group by country
  GROUP BY country
  -- Filter results to countries with more than 10 different certifications
  HAVING COUNT(DISTINCT certification) > 10;

### HAVING and sorting

Filtering and sorting go hand in hand and gives you greater interpretability by ordering our results.

Let's see this magic at work by writing a query showing what countries have the highest average film budgets.

Select the country and the average budget as average_budget, rounded to two decimal, from films.
Group the results by country.
Filter the results to countries with an average budget of more than one billion (1000000000).
Sort by descending order of the average_budget.

  -- Select the country and average_budget from films
  SELECT country, ROUND(AVG(budget), 2) AS average_budget
  FROM films
  -- Group by country
  GROUP BY country
  -- Filter to countries with an average_budget of more than one billion
  HAVING ROUND(AVG(budget), 2) > 1000000000
  -- Order by descending order of the aggregated budget
  ORDER BY average_budget DESC;

### All together now

It's time to use much of what you've learned in one query! This is good preparation for using SQL in the real world where you'll often be asked to write more complex queries since some of the basic queries can be answered by playing around in spreadsheet applications.

In this exercise, you'll write a query that returns the average budget and gross earnings for films each year after 1990 if the average budget is greater than 60 million.

This will be a big query, but you can handle it!

Select the release_year for each film in the films table, filter for records released after 1990, and group by release_year.

Select the release_year for each film in the films table, filter for records released after 1990, and group by release_year.
-- Select the release_year for films released after 1990 grouped by year

  SELECT release_year
  FROM films
  WHERE release_year > 1990
  GROUP BY release_year;

Modify the query to include the average budget aliased as avg_budget and average gross aliased as avg_gross for the results we have so far.
-- Modify the query to also list the average budget and average gross

  SELECT release_year, AVG(budget) AS avg_budget, AVG(gross) AS avg_gross
  FROM films
  WHERE release_year > 1990
  GROUP BY release_year;

Modify the query once more so that only years with an average budget of greater than 60 million are included.
SELECT release_year, AVG(budget) AS avg_budget, AVG(gross) AS avg_gross

  FROM films
  WHERE release_year > 1990
  GROUP BY release_year
  -- Modify the query to see only years with an avg_budget of more than 60 million
  HAVING AVG(budget) > 60000000;

Finally, order the results from the highest average gross and limit to one.

  SELECT release_year, AVG(budget) AS avg_budget, AVG(gross) AS avg_gross
  FROM films
  WHERE release_year > 1990
  GROUP BY release_year
  HAVING AVG(budget) > 60000000
  -- Order the results from highest to lowest average gross and limit to one
  ORDER BY avg_gross DESC
  LIMIT 1;

##  Summary

In this video, we recap the key SQL concepts covered throughout the course. Here's what you learned:

Chapter 1: COUNT and LIMIT keywords
Chapter 2: Filtering techniques, IS NULL, IS NOT NULL
Chapter 3: ROUND and aggregate functions
Chapter 4: ORDER BY, DESC, GROUP BY, HAVING
You also explored comparison operators, arithmetic, error handling, and debugging. Your new skills enable you to select, query, filter, summarize, sort, and group data effectively.

Here's a quick SQL snippet to reinforce your learning:

SELECT COUNT(*)
FROM employees
WHERE department IS NOT NULL
GROUP BY department
HAVING COUNT(*) > 5;
Keep practicing and building your SQL expertise!
