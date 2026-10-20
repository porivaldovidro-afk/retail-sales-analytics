# Intermediate SQL Part 3

In this lesson, you'll explore summarizing data using SQL's aggregate functions. These functions help analyze datasets by performing calculations on multiple values and returning a single result. You'll learn four key functions:

AVG(): Finds the average of a field.
SUM(): Calculates the total sum of a field.
MIN(): Identifies the smallest value.
MAX(): Identifies the largest value.
These functions follow the SELECT statement and can be used with both numerical and non-numerical fields. Here's a quick example:

SELECT AVG(budget) AS average_budget FROM films;

Now, dive into the exercises to practice using these functions!

## Aggregate functions and data types

Aggregate functions are another valuable tool for the SQL programmer. They are used extensively across businesses to calculate important metrics, such as the average cost of making a film.

You know five different aggregate functions:

-AVG()

-SUM()

-MIN()

-MAX()

-COUNT()

## Test your knowledge of what data types they are compatible with.

Practice with aggregate functions
Now let's try extracting summary information from a table using these new aggregate functions. Summarizing is helpful in real life when extracting top-line details from your dataset. Perhaps you'd like to know how old the oldest film in the films table is, what the most expensive film is, or how many films you have listed.

Now it's your turn to get more insights about the films table!

Use the SUM() function to calculate the total duration of all films and alias with total_duration.
-- Query the sum of film durations

    SELECT SUM(duration) AS total_duration
    FROM films

Calculate the average duration of all films and alias with average_duration.
-- Calculate the average duration of all films

    SELECT AVG(duration) AS average_duration
    FROM films


Find the most recent release_year in the films table, aliasing as latest_year.
-- Find the latest release_year

    SELECT MAX(release_year) AS latest_year
    FROM films

Find the duration of the shortest film and use the alias shortest_film.
-- Find the duration of the shortest film

    SELECT MIN(duration) AS shortest_film
    FROM films

## PART 2

In this lesson, you'll enhance your data analysis skills by combining filtering with summarizing techniques. You'll learn how to use aggregate functions alongside the WHERE clause for deeper insights.

Calculate averages with the AVG function
Use SUM to find total values
Identify minimum and maximum values with MIN and MAX
Count non-missing entries using COUNT
Round numbers with ROUND() for cleaner results
Here's a quick example:

SELECT ROUND(AVG(budget), 2) 
FROM films 
WHERE release_year >= 2010;

This query calculates the average budget of films released in 2010 or later, rounded to two decimal places.

### Combining aggregate functions with WHERE

When combining aggregate functions with WHERE, you get a powerful tool that allows you to get more granular with your insights, for example, to get the total budget of movies made from the year 2010 onwards.

This combination is useful when you only want to summarize a subset of your data. In your film-industry role, as an example, you may like to summarize each certification category to compare how they each perform or if one certification has a higher average budget than another.

Let's see what insights you can gain about the financials in the dataset.

Use SUM() to calculate the total gross for all films made in the year 2000 or later, and use the alias total_gross.
-- Calculate the sum of gross from the year 2000 or later

    SELECT SUM(gross) AS total_gross
    FROM films
    WHERE release_year >= 2000;

Calculate the average amount grossed by all films whose titles start with the letter 'A' and alias with avg_gross_A.
-- Calculate the average gross of films that start with A
   
    SELECT AVG(gross) AS avg_gross_A
    FROM films
    WHERE title LIKE 'A%';

Calculate the lowest gross film in 1994 and use the alias lowest_gross.
-- Calculate the lowest gross film in 1994

    SELECT MIN(gross) AS lowest_gross
    FROM films
    WHERE release_year = 1994

Calculate the highest gross film between 2000 and 2012, inclusive, and use the alias highest_gross.
-- Calculate the highest gross film released between 2000-2012

    SELECT MAX(gross) AS highest_gross
    FROM films
    WHERE release_year BETWEEN 2000 AND 2012;

### Using ROUND()

Aggregate functions work great with numerical values; however, these results can sometimes get unwieldy when dealing with long decimal values. Luckily, SQL provides you with the ROUND() function to tame these long decimals.

If asked to give the average budget of your films, ten decimal places is not necessary. Instead, you can round to two decimal places to create results that make more sense for currency.

Now you try!

Calculate the average facebook_likes to one decimal place and assign to the alias, avg_facebook_likes.
-- Round the average number of facebook_likes to one decimal place

    SELECT ROUND(AVG(facebook_likes), 1) AS avg_facebook_likes
    FROM reviews;

### ROUND() with a negative parameter

A useful thing you can do with ROUND() is have a negative number as the decimal place parameter. This can come in handy if your manager only needs to know the average number of facebook_likes to the hundreds since granularity below one hundred likes won't impact decision making.

Social media plays a significant role in determining success. If a movie trailer is posted and barely gets any likes, the movie itself may not be successful. Remember how 2020's "Sonic the Hedgehog" movie got a revamp after the public saw the trailer?

Let's apply this to other parts of the dataset and see what the benchmark is for movie budgets so, in the future, it's clear whether the film is above or below budget.

Calculate the average budget from the films table, aliased as avg_budget_thousands, and round to the nearest thousand.
-- Calculate the average budget rounded to the thousands

    SELECT ROUND(AVG(budget), -3) AS avg_budget_thousands
    FROM films;

## PART 3

In this lesson, you'll dive into using arithmetic in SQL queries and the importance of aliasing with AS. Key points include:

Performing arithmetic operations: addition, subtraction, multiplication, and division.
Understanding integer division and using decimals for precision.
Differentiating between arithmetic operations and aggregate functions.
Using aliases to clarify field names, especially when summarizing data or using multiple functions.
Here's a quick example to illustrate:

SELECT (gross - budget) AS profit
FROM films;

This query calculates the profit by subtracting the budget from the gross and uses an alias for clarity. Remember, aliases can't be used in the WHERE clause due to SQL's execution order.

### Aliasing with functions

Aliasing can be a lifesaver, especially as we start to do more complex SQL queries with multiple criteria. Aliases help you keep your code clean and readable. For example, if you want to find the MAX() value of several fields without aliasing, you'll end up with the result with several columns called max and no idea which is which. You can fix this with aliasing.

Now, it's over to you to clean up the following queries.

Select the title and duration in hours for all films and alias as duration_hours; since the current durations are in minutes, you'll need to divide duration by 60.0.
-- Calculate the title and duration_hours from films
    
    SELECT title, (duration / 60.0) AS duration_hours
    FROM films;

Calculate the percentage of people who are no longer alive and alias the result as percentage_dead.
-- Calculate the percentage of people who are no longer alive

    SELECT (COUNT(deathdate) * 100.0 / COUNT(*)) AS percentage_dead
    FROM people;


Find how many decades (period of ten years) the films table covers by using MIN() and MAX(); alias as number_of_decades.
-- Find the number of decades in the films table

    SELECT ((MAX(release_year) - MIN(release_year)) / 10) AS number_of_decades
    FROM films;

### Rounding results

You found some valuable insights in the previous exercise, but many of the results were inconveniently long. We forgot to round! We won't make you redo them all; however, you'll update the worst offender in this exercise.

Update the query by adding ROUND() around the calculation and round to two decimal places.
-- Round duration_hours to two decimal places

    SELECT title, ROUND((duration / 60.0), 2) AS duration_hours
    FROM films;

