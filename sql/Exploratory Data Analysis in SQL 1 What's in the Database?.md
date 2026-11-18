# What's in the Database?

Start exploring a database by identifying the tables and the foreign keys that link them. Look for missing values, count the number of observations, and join tables to understand how they're related. Learn about coalescing and casting data along the way.

## Count missing values
Which column of fortune500 has the most missing values? To find out, you'll need to check each column individually, although here we'll check just two: ticker and industry.

Course Note: While you're unlikely to encounter this issue during this exercise, note that if you run a query that takes more than a few seconds to execute, your session may expire or you may be disconnected from the server. You will not have this issue with any of the exercise solutions, so if your session expires or disconnects, there's an error with your query.

### Instructions 
1. Subtract the count of the non-null ticker values from the total number of rows in fortune500; alias the difference as missing

    -- Count the number of null values in the ticker column
    SELECT count(*) - COUNT(ticker) AS missing
    FROM fortune500;

2. Repeat for the industry column: subtract the count of the non-null industry values from the total number of rows in fortune500; alias the difference as missing

    -- Count the number of null values in the industry column

    SELECT COUNT(*) - COUNT(industry) AS missing
    FROM fortune500;


## Join tables
Part of exploring a database is figuring out how tables relate to each other. The company and fortune500 tables don't have a formal relationship between them in the database, but this doesn't prevent you from joining them.

To join the tables, you need to find a column that they have in common where the values are consistent across the tables. Remember: just because two tables have a column with the same name, it doesn't mean those columns necessarily contain compatible data. If you find more than one pair of columns with similar data, you may need to try joining with each in turn to see if you get the same number of results.

Reference the entity relationship diagram if needed.

### Instructions
Closely inspect the contents of the company and fortune500 tables to find a column present in both tables that can also be considered to uniquely identify each company.
Join the company and fortune500 tables with an INNER JOIN.

    SELECT company.name
    -- Table(s) to select from
    FROM company
        INNER JOIN fortune500
        ON fortune500.ticker=company.ticker;

## Foreign keys
Recall that foreign keys reference another row in the database via a unique ID. Values in a foreign key column are restricted to values in the referenced column OR NULL.

Using what you know about foreign keys, why can't the tag column in the tag_type table be a foreign key that references the tag column in the stackoverflow table?

Remember, you can reference the slides using the icon in the upper right of the screen to review the requirements for a foreign key.

## Read an entity relationship diagram
The information you need is sometimes split across multiple tables in the database.

What is the most common stackoverflow tag_type? What companies have a tag of that type?

To generate a list of such companies, you'll need to join three tables together.

Reference the entity relationship diagram as needed when determining which columns to use when joining tables

### Instructions
First, using the tag_type table, count the number of tags with each type.
Order the results to find the most common tag type.

    -- Count the number of tags with each type
    SELECT type, COUNT(*) AS count
    FROM tag_type
    -- To get the count for each type, what do you need to do?
    GROUP BY type
    -- Order the results with the most common tag types listed first
    ORDER BY count DESC;

Join the tag_company, company, and tag_type tables, keeping only mutually occurring records.
Select company.name, tag_type.tag, and tag_type.type for tags with the most common type from the previous step.

    -- Select the 3 columns desired
    SELECT company.name, tag_type.tag, tag_type.type
    FROM company
        -- Join to the tag_company table
        INNER JOIN tag_company 
        ON company.id = tag_company.company_id
        -- Join to the tag_type table
        INNER JOIN tag_type
        ON tag_company.tag = tag_type.tag
    -- Filter to most common type
    WHERE type='cloud';


## Coalesce
The coalesce() function can be useful for specifying a default or backup value when a column contains NULL values.

coalesce() checks arguments in order and returns the first non-NULL value, if one exists.

coalesce(NULL, 1, 2) = 1
coalesce(NULL, NULL) = NULL
coalesce(2, 3, NULL) = 2
In the fortune500 data, industry contains some missing values. Use coalesce() to use the value of sector as the industry when industry is NULL. Then find the most common industry.

Use coalesce() to select the first non-NULL value from industry, sector, or 'Unknown' as a fallback value.
Alias the result of the call to coalesce() as industry2.
Count the number of rows with each industry2 value.
Find the most common value of industry2.

    -- Use coalesce
    SELECT COALESCE(industry, sector, 'Unknown') AS industry2,
        -- Don't forget to count!
        COUNT(*) AS count
    FROM fortune500 
    -- Group by what? (What are you counting by?)
    GROUP BY industry2
    -- Order results to see most common first
    ORDER BY count DESC
    -- Limit results to get just the one value you want
    LIMIT 1;


## Effects of casting
When you cast data from one type to another, information can be lost or changed. See how the casting changes values and practice casting data using the CAST() function and the :: syntax.

SELECT CAST(value AS new_type);

SELECT value::new_type;

### Instructions

1. Select profits_change and profits_change cast as integer from fortune500.
Look at how the values were converted.

    -- Select the original value
    SELECT profits_change, 
        -- Cast profits_change
        CAST(profits_change AS integer) AS profits_change_int
    FROM fortune500;

2. Compare the results of casting of dividing the integer value 10 by 3 to the result of dividing the numeric value 10 by 3.

    -- Divide 10 by 3
    SELECT 10/3, 
        -- Cast 10 as numeric and divide by 3
        10::numeric/3;

3. Now cast numbers that appear as text as numeric.
Note: 1e3 is scientific notation.

    SELECT '3.2'::numeric,
        '-123'::numeric,
        '1e3'::numeric,
        '1e-3'::numeric,
        '02314'::numeric,
        '0002'::numeric;

## Summarize the distribution of numeric values
Was 2017 a good or bad year for revenue of Fortune 500 companies? Examine how revenue changed from 2016 to 2017 by first looking at the distribution of revenues_change and then counting companies whose revenue increased.

### Instructions

1. Use GROUP BY and count() to examine the values of revenues_change.
Order the results by revenues_change to see the distribution.

    -- Select the count of each value of revenues_change
    SELECT revenues_change, COUNT(*) AS count
    FROM fortune500
    GROUP BY revenues_change
    -- order by the values of revenues_change
    ORDER BY count;

2. Repeat step 1, but this time, cast revenues_change as an integer to reduce the number of different values.

    -- Select the count of each revenues_change integer value
    SELECT revenues_change::integer, COUNT(*) AS count
    FROM fortune500
    GROUP BY revenues_change::integer
    -- order by the values of revenues_change
    ORDER BY count;

3. How many of the Fortune 500 companies had revenues increase in 2017 compared to 2016? To find out, count the rows of fortune500 where revenues_change indicates an increase.

    -- Count rows 
    SELECT COUNT(*)
    FROM fortune500
    -- Where...
    WHERE revenues_change > 0;