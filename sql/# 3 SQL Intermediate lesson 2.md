# 3 SQL Intermediate lesson 2

perhaps - talvez

In this lesson, you'll learn how to filter data using the WHERE clause in SQL, focusing on numbers and comparison operators. Here's what you'll cover:

    Use WHERE to filter data relevant to specific questions.
    Apply comparison operators like greater than, less than, and equals.
    Understand how to filter data using not equal to.
    Learn the order of SQL query execution with WHERE and LIMIT.

Example SQL query:

SELECT title 
FROM films 
WHERE release_year > 1960;

This query selects films released after 1960. Now, dive into the exercises to practice these concepts!

## Using WHERE with numbers

Filtering with WHERE allows you to analyze your data better. You may have a dataset that includes a range of different movies, and you need to do a case study on the most notable films with the biggest budgets. In this case, you'll want to filter your data to a specific budget range.

Now it's your turn to use the WHERE clause to filter numeric values!

-- Select film_id and imdb_score with an imdb_score over 7.0
	SELECT film_id, imdb_score
	FROM reviews
	WHERE imdb_score > 7.0;

-- Select film_id and facebook_likes for ten records with less than 1000 likes 
	SELECT film_id, facebook_likes
	FROM reviews
	WHERE facebook_likes < 1000
	LIMIT 10;

-- Count the records with at least 100,000 votes
	SELECT COUNT(num_votes) AS films_over_100K_votes
	FROM reviews
	WHERE num_votes >= 100000;


## Using WHERE with text

WHERE can also filter string values.

Imagine you are part of an organization that gives cinematography awards, and you have several international categories. Before you confirm an award for every language listed in your dataset, it may be worth seeing if there are enough films of a specific language to make it a fair competition. If there is only one movie or a significant skew, it may be worth considering a different way of giving international awards.

Let's try this out!

-- Count the Spanish-language films
	SELECT COUNT(language) AS count_spanish
	FROM films
	WHERE language = 'Spanish';

## PART 2

In this lesson, you'll enhance your SQL filtering skills by learning to apply multiple criteria using OR, AND, and BETWEEN. These keywords help refine your data queries for more precise results.

    OR: Use when at least one condition must be met.
    AND: Use when all conditions must be satisfied.
    BETWEEN: Use for filtering within a range, inclusive of boundaries.

Here's a quick example:

SELECT title
FROM films
WHERE (release_year = 1994 OR release_year = 1995)
AND (certification = 'PG' OR certification = 'R');

Try using these operators to query films released between 1994 and 2000.

## Using AND

The following exercises combine AND and OR with the WHERE clause. Using these operators together strengthens your queries and analyses of data.

You will apply these new skills now on the films table.


-- Select the title and release_year for all German-language films released before 2000
	SELECT title, release_year
	FROM films 
	WHERE language = 'German' AND 	release_year < 2000;


-- Update the query to see all German-language films released after 2000
	SELECT title, release_year
	FROM films
	WHERE release_year > 2000
		AND language = 'German';

-- Select all records for German-language films released after 2000 and before 2010
	SELECT *
	FROM films
	WHERE language = 'German' AND release_year > 2000 AND release_year < 2010;


## Using OR

This time you'll write a query to get the title and release_year of films released in 1990 or 1999, which were in English or Spanish and took in more than $2,000,000 gross.

It looks like a lot, but you can build the query up one step at a time to get comfortable with the underlying concept in each step. Let's go!

-- Find the title and year of films from the 1990 or 1999
	SELECT title, release_year
	FROM films
	WHERE release_year = 1990 OR release_year = 1999;

***********************************************************
SELECT title, release_year
FROM films
WHERE (release_year = 1990 OR release_year = 1999)
-- Add a filter to see only English or Spanish-language films
	AND (language = 'English' OR language = 'Spanish');

***********************************************************

SELECT title, release_year
FROM films
WHERE (release_year = 1990 OR release_year = 1999)
	AND (language = 'English' OR language = 'Spanish')
-- Filter films with more than $2,000,000 gross
	AND gross > 2000000;


## Using BETWEEN

Let's use BETWEEN with AND on the films database to get the title and release_year of all Spanish-language films released between 1990 and 2000 (inclusive) with budgets over $100 million.

We have broken the problem into smaller steps so that you can build the query as you go along!

-- Select the title and release_year for films released between 1990 and 2000

	SELECT title, release_year
	FROM films

***********************************************************

	SELECT title, release_year
	FROM films
	WHERE release_year BETWEEN 1990 AND 2000
	-- Narrow down your query to films with budgets > $100 million
		AND budget > 100000000;

***********************************************************

	SELECT title, release_year
	FROM films
	WHERE release_year BETWEEN 1990 AND 2000
	AND budget > 100000000
	-- Restrict the query to only Spanish-language films
	AND language = 'Spanish';

***********************************************************

	SELECT title, release_year
	FROM films
	WHERE release_year BETWEEN 1990 AND 2000
		AND budget > 100000000
	-- Amend the query to include Spanish or French-language films
		AND (language = 'Spanish' OR language = 'French');

## PART 3

In this lesson, you'll learn how to filter textual data using SQL. We move beyond exact matches to searching for patterns with new keywords: LIKE, NOT LIKE, and IN.

    LIKE: Use wildcards (%) for multiple characters and (_) for a single character to match patterns (Padrões).
    NOT LIKE: Exclude patterns from your search.
    IN: Simplify multiple OR conditions in your queries.

Example SQL code:

SELECT name FROM people WHERE name LIKE 'A%';

This query finds names starting with 'A'. Practice these techniques to enhance your data querying skills!

### LIKE and NOT LIKE

The LIKE and NOT LIKE operators can be used to find records that either match or do not match a specified pattern, respectively. They can be coupled with the wildcards % and _. The % will match zero or many characters, and _ will match a single character.

This is useful when you want to filter text, but not to an exact word.

Do the following exercises to gain some practice with these keywords.

-- Select the names that start with B

	SELECT name
	FROM people
	WHERE name LIKE 'B%';

***********************************************************

	SELECT name
	FROM people
	-- Select the names that have r as the second letter
	WHERE name LIKE '_r%'

***********************************************************

	SELECT name
	FROM people
	-- Select names that don't start with A
	WHERE name NOT LIKE 'A%';

### WHERE IN

You now know you can query multiple conditions using the IN operator and a set of parentheses. It is a valuable piece of code that helps us keep our queries clean and concise.

Try using the IN operator yourself!

-- Find the title and release_year for all films over two hours in length released in 1990 and 2000

	SELECT title, release_year
	FROM films
	WHERE release_year IN (1990, 2000) AND duration > 120;

***********************************************************

-- Find the title and language of all films in English, Spanish, and French

	SELECT title, language
	FROM films
	WHERE language IN ('English', 'Spanish', 'French');

***********************************************************

-- Find the title, certification, and language all films certified NC-17 or R that are in English, Italian, or Greek

	SELECT title, certification, language
	FROM films
	WHERE certification IN ('NC-17', 'R') AND language IN ('English', 'Italian', 'Greek');	

### Combining filtering and selecting

Time for a little challenge. So far, your SQL vocabulary from this course includes COUNT(), DISTINCT, LIMIT, WHERE, OR, AND, BETWEEN, LIKE, NOT LIKE, and IN. In this exercise, you will try to use some of these together. Writing more complex queries will be standard for you as you become a qualified SQL programmer.

As this query will be a little more complicated than what you've seen so far, we've included a bit of code to get you started. You will be using DISTINCT here too because, surprise, there are two movies named 'Hamlet' in this dataset!

Follow the instructions to find out what 90's films we have in our dataset that would be suitable for English-speaking teens.

-- Count the unique titles
SELECT COUNT(DISTINCT title) AS nineties_english_films_for_teens
FROM films
-- Filter to release_years to between 1990 and 1999
WHERE release_year BETWEEN 1990 AND 1999
-- Filter to English-language films
	AND language = 'English'
-- Narrow it down to G, PG, and PG-13 certifications
	AND certification IN ('G', 'PG', 'PG-13');


## PART 4

In this lesson, you'll explore how to handle NULL values in SQL, which represent missing or unknown data. Understanding NULLs is crucial for accurate data analysis.

    NULL indicates missing or unknown values
    Use IS NULL to find missing data
    Use IS NOT NULL to filter out missing values
    COUNT with a field name vs. COUNT with IS NOT NULL yields the same result

Example SQL query:

SELECT COUNT(*) 
FROM people 
WHERE birthdate IS NULL;

This query counts entries with missing birthdates. Mastering these techniques will enhance your data handling skills.

### Practice with NULLs

Well done. Now that you know what NULL means and what it's used for, it's time for some more practice!

Let's explore the films table again to better understand what data you have.

-- List all film titles with missing budgets

	SELECT title AS no_budget_info
	FROM films
	WHERE budget IS NULL;

-- Count the number of films we have language data for

	SELECT COUNT(*) AS count_language_known
	FROM films
	WHERE language IS NOT NULL;

