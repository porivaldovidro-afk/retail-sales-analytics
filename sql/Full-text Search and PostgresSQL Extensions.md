An introduction into some more advanced capabilities of PostgreSQL like full-text search and extensions

## A review of the LIKE operator
The LIKE operator allows us to filter our queries by matching one or more characters in text data. By using the % wildcard we can match one or more characters in a string. This is useful when you want to return a result set that matches certain characteristics and can also be very helpful during exploratory data analysis or data cleansing tasks.

Let's explore how different usage of the % wildcard will return different results by looking at the film table of the Sakila DVD Rental dat

Select all columns for all records that begin with the word GOLD.

    -- Select all columns
    SELECT *
    FROM film
    -- Select only records that begin with the word 'GOLD'
    WHERE title LIKE 'GOLD%';

Now select all records that end with the word GOLD.

    SELECT *
    FROM film
    -- Select only records that end with the word 'GOLD'
    WHERE title LIKE '%GOLD';

Finally, select all records that contain the word 'GOLD'.

    SELECT *
    FROM film
    -- Select only records that contain the word 'GOLD'
    WHERE title LIKE '%GOLD%';

## What is a tsvector?
You saw how to convert strings to tsvector and tsquery in the video and, in this exercise, we are going to dive deeper into what these functions actually return after converting a string to a tsvector. In this example, you will convert a text column from the film table to a tsvector and inspect the results. Understanding how full-text search works is the first step in more advanced machine learning and data science concepts like natural language processing.

Select the film description and convert it to a tsvector data type.

    -- Select the film description as a tsvector
    SELECT to_tsvector(description)
    FROM film;

## Basic full-text search
Searching text will become something you do repeatedly when building applications or exploring data sets for data science. Full-text search is helpful when performing exploratory data analysis for a natural language processing model or building a search feature into your application.

In this exercise, you will practice searching a text column and match it against a string. The search will return the same result as a query that uses the LIKE operator with the % wildcard at the beginning and end of the string, but will perform much better and provide you with a foundation for more advanced full-text search queries. Let's dive in.

Select the title and description columns from the film table.
Perform a full-text search on the title column for the word elf

    -- Select the title and description
    SELECT title, description
    FROM film
    -- Convert the title to a tsvector and match it against the tsquery 
    WHERE to_tsvector(title) @@ to_tsquery('elf');

## User-defined data types
ENUM or enumerated data types are great options to use in your database when you have a column where you want to store a fixed list of values that rarely change. Examples of when it would be appropriate to use an ENUM include days of the week and states or provinces in a country.

Another example can be the directions on a compass (i.e., north, south, east and west.) In this exercise, you are going to create a new ENUM data type called compass_position.

Create a new enumerated data type called compass_position.
Use the four positions of a compass as the values.

    -- Create an enumerated data type, compass_position
    CREATE TYPE compass_position AS ENUM (
        -- Use the four cardinal directions
        'north', 
        'south',
        'east', 
        'west'
    );

Verify that the new data type has been created by looking in the pg_type system table.

    -- Create an enumerated data type, compass_position
    CREATE TYPE compass_position AS ENUM (
        -- Use the four cardinal directions
        'North', 
        'South',
        'East', 
        'West'
    );
    -- Confirm the new data type is in the pg_type system table
    SELECT typname
    FROM pg_type
    WHERE typname='compass_position';

## Getting info about user-defined data types
The Sakila database has a user-defined enum data type called mpaa_rating. The rating column in the film table is an mpaa_rating type and contains the familiar rating for that film like PG or R. This is a great example of when an enumerated data type comes in handy. Film ratings have a limited number of standard values that rarely change.

When you want to learn about a column or data type in your database the best place to start is the INFORMATION_SCHEMA. You can find information about the rating column that can help you learn about the type of data you can expect to find. For enum data types, you can also find the specific values that are valid for a particular enum by looking in the pg_enum system table. Let's dive into the exercises and learn more.

    -- Select the column name, data type and udt name columns
    SELECT column_name, data_type, udt_name
    FROM INFORMATION_SCHEMA.COLUMNS 
    -- Filter by the rating column in the film table
    WHERE table_name ='film' AND column_name='rating';

Select all columns from the pg_type table where the type name is equal to mpaa_rating.

    SELECT *
    FROM pg_type 
    WHERE typname='mpaa_rating'

## User-defined functions in Sakila
If you were running a real-life DVD Rental store, there are many questions that you may need to answer repeatedly like whether a film is in stock at a particular store or the outstanding balance for a particular customer. These types of scenarios are where user-defined functions will come in very handy. The Sakila database has several user-defined functions pre-defined. These functions are available out-of-the-box and can be used in your queries like many of the built-in functions we've learned about in this course.

In this exercise, you will build a query step-by-step that can be used to produce a report to determine which film title is currently held by which customer using the inventory_held_by_customer() function.

Select the title and inventory_id columns from the film and inventory tables in the database.


    -- Select the film title and inventory ids
    SELECT 
        f.title, 
        i.inventory_id
    FROM film AS f 
        -- Join the film table to the inventory table
        INNER JOIN inventory AS i ON f.film_id=i.film_id

inventory_id is currently held by a customer and alias the column as held_by_cust

    -- Select the film title, rental and inventory ids
    SELECT 
        f.title, 
        i.inventory_id,
        -- Determine whether the inventory is held by a customer
        inventory_held_by_customer(i.inventory_id) AS held_by_cust
    FROM film as f 
        -- Join the film table to the inventory table
        INNER JOIN inventory AS i ON f.film_id=i.film_id 

Now filter your query to only return records where the inventory_held_by_customer() function returns a non-null value.

    -- Select the film title and inventory ids
    SELECT 
        f.title, 
        i.inventory_id,
        -- Determine whether the inventory is held by a customer
        inventory_held_by_customer(i.inventory_id) as held_by_cust
    FROM film as f 
        INNER JOIN inventory AS i ON f.film_id=i.film_id 
    WHERE
        -- Only include results where the held_by_cust is not null
        inventory_held_by_customer(i.inventory_id) IS NOT NULL;

## Enabling extensions
Before you can use the capabilities of an extension it must be enabled. As you have previously learned, most PostgreSQL distributions come pre-bundled with many useful extensions to help extend the native features of your database. You will be working with fuzzystrmatch and pg_trgm in upcoming exercises but before you can practice using the capabilities of these extensions you will need to first make sure they are enabled in our database. In this exercise you will enable the pg_trgm extension and confirm that the fuzzystrmatch extension, which was enabled in the video, is still enabled by querying the pg_extension system table.

Enable the pg_trgm extension

    -- Enable the pg_trgm extension
    CREATE EXTENSION IF NOT EXISTS pg_trgm;

Now confirm that both fuzzystrmatch and pg_trgm are enabled by selecting all rows from the appropriate system table.

    -- Select all rows extensions
    SELECT * 
    FROM  pg_extension;

## Measuring similarity between two strings
Now that you have enabled the fuzzystrmatch and pg_trgm extensions you can begin to explore their capabilities. First, we will measure the similarity between the title and description from the film table of the Sakila database.

Select the film title and description.
Calculate the similarity between the title and description.

    -- Select the title and description columns
    SELECT 
    title, 
    description, 
    -- Calculate the similarity
    similarity(title, description)
    FROM 
    film

## Levenshtein distance examples
Now let's take a closer look at how we can use the levenshtein function to match strings against text data. If you recall, the levenshtein distance represents the number of edits required to convert one string to another string being compared.

In a search application or when performing data analysis on any data that contains manual user input, you will always want to account for typos or incorrect spellings. The levenshtein function provides a great method for performing this task. In this exercise, we will perform a query against the film table using a search string with a misspelling and use the results from levenshtein to determine a match. Let's check it out.

Select the film title and film description.
Calculate the levenshtein distance for the film title with the string JET NEIGHBOR.

    -- Select the title and description columns
    SELECT  
    title, 
    description, 
    -- Calculate the levenshtein distance
    levenshtein(title, 'JET NEIGHBOR') AS distance
    FROM 
    film
    ORDER BY 3

## Putting it all together
In this exercise, we are going to use many of the techniques and concepts we learned throughout the course to generate a data set that we could use to predict whether the words and phrases used to describe a film have an impact on the number of rentals.

First, you need to create a tsvector from the description column in the film table. You will match against a tsquery to determine if the phrase "Astounding Drama" leads to more rentals per month. Next, create a new column using the similarity function to rank the film descriptions based on this phrase.

Select the title and description for all DVDs from the film table.
Perform a full-text search by converting the description to a tsvector and match it to the phrase 'Astounding & Drama' using a tsquery in the WHERE clause.

    -- Select the title and description columns
    SELECT  
    title, 
    description 
    FROM 
    film
    WHERE 
    -- Match "Astounding Drama" in the description
    to_tsvector(description) @@ 
    to_tsquery('Astounding & Drama');

Add a new column that calculates the similarity of the description with the phrase 'Astounding Drama'.
Sort the results by the new similarity column in descending order.

    SELECT 
    title, 
    description, 
    -- Calculate the similarity
    similarity(description, 'Astounding Drama')
    FROM 
    film 
    WHERE 
    to_tsvector(description) @@ 
    to_tsquery('Astounding & Drama') 
    ORDER BY 
        similarity(description, 'Astounding Drama') DESC;

