Text, or character, data can get messy, but you'll learn how to deal with inconsistencies in case, spacing, and delimiters. Learn how to use a temporary table to recode messy categorical data to standardized values you can count and aggregate. Extract new variables from unstructured text as you explore help requests submitted to the city of Evanston, IL.

# Count the categories
In this chapter, we'll be working mostly with the Evanston 311 data in table evanston311. This is data on help requests submitted to the city of Evanston, IL.

This data has several character columns. Start by examining the most frequent values in some of these columns to get familiar with the common categories.

## Instructions

1. How many rows does each priority level have?

    -- Select the count of each level of priority
    SELECT priority, count(*)
    FROM evanston311
    GROUP BY priority;

2. How many distinct values of zip appear in at least 100 rows?

    -- Find values of zip that appear in at least 100 rows
    -- Also get the count of each value
    SELECT distinct zip, count(*)
    FROM evanston311
    GROUP BY zip
    HAVING count(*) >= 100;

3. How many distinct values of source appear in at least 100 rows?

    -- Find values of source that appear in at least 100 rows
    -- Also get the count of each value
    SELECT distinct source, count(*)
    FROM evanston311
    GROUP BY source
    HAVING count(*) >= 100;

4. Select the five most common values of street and the count of each.

    -- Find the 5 most common values of street and the count of each
    SELECT street, count(*)
    FROM evanston311
    GROUP BY street
    ORDER BY count(*) DESC
    LIMIT 5;

# Spotting character data problems
Explore the distinct values of the street column. Select each street value and the count of the number of rows with that value. Sort the results by street to see similar values near each other.

Look at the results.

Which of the following is NOT an issue you see with the values of street?

# Trimming
Some of the street values in evanston311 include house numbers with # or / in them. In addition, some street values end in a ..

Remove the house numbers, extra punctuation, and any spaces from the beginning and end of the street values as a first attempt at cleaning up the values.

Trim digits 0-9, #, /, ., and spaces from the beginning and end of street.
Select distinct original street value and the corrected street value.
Order the results by the original street value.

    SELECT distinct street,
        -- Trim off unwanted characters from street
        trim(street, '0123456789 #/.') AS cleaned_street
    FROM evanston311
    ORDER BY street;

# Exploring unstructured text
The description column of evanston311 has the details of the inquiry, while the category column groups inquiries into different types. How well does the category capture what's in the description?

LIKE and ILIKE queries will help you find relevant descriptions and categories. Remember that with LIKE queries, you can include a % on each side of a word to find values that contain the word. For example:

SELECT category
  FROM evanston311
 WHERE category LIKE '%Taxi%';

% matches 0 or more characters.

Building up the query through the steps below, find inquires that mention trash or garbage in the description without trash or garbage being in the category. What are the most frequent categories for such inquiries?

## Instructions

1. Use ILIKE to count rows in evanston311 where the description contains 'trash' or 'garbage' regardless of case.

    -- Count rows
    SELECT count(*)
    FROM evanston311
    -- Where description includes trash or garbage
    WHERE description ILIKE '%trash%'
        OR description ILIKE '%garbage%';

2. category values are in title case. Use LIKE to find category values with 'Trash' or 'Garbage' in them.

    -- Select categories containing Trash or Garbage
    SELECT category
    FROM evanston311
    -- Use LIKE
    WHERE category LIKE '%Trash%'
        OR category LIKE '%Garbage%';

3. Count rows where the description includes 'trash' or 'garbage' but the category does not.

    -- Count rows
    SELECT count(*)
    FROM evanston311 
    -- description contains trash or garbage (any case)
    WHERE (description ILIKE '%garbage%'
        OR description ILIKE '%trash%') 
    -- category does not contain Trash or Garbage
    AND category NOT ILIKE '%trash%'
    AND category NOT ILIKE '%garbage%';

4. Find the most common categories for rows with a description about trash that don't have a trash-related category.

    -- Count rows with each category
    SELECT category, count(*)
    FROM evanston311 
    WHERE (description ILIKE '%trash%'
        OR description ILIKE '%garbage%') 
    AND category NOT LIKE '%Trash%'
    AND category NOT LIKE '%Garbage%'
    -- What are you counting?
    GROUP BY category
    ORDER BY count DESC
    LIMIT 10;

# Concatenate strings
House number (house_num) and street are in two separate columns in evanston311. Concatenate them together with concat() with a space in between the values.

Concatenate house_num, a space ' ', and street into a single value using the concat().
Use a trim function to remove any spaces from the start of the concatenated value.

    -- Concatenate house_num, a space, and street and trim spaces from the start of the result
    SELECT ltrim(concat(house_num, ' ', street)) AS address
    FROM evanston311;

# Split strings on a delimiter
The street suffix is the part of the street name that gives the type of street, such as Avenue, Road, or Street. In the Evanston 311 data, sometimes the street suffix is the full word, while other times it is the abbreviation.

Extract just the first word of each street value to find the most common streets regardless of the suffix.

To do this, use

split_part(string_to_split, delimiter, part_number)

Use split_part() to select the first word in street; alias the result as street_name.
Also select the count of each value of street_name

    -- Select the first word of the street value
    SELECT split_part(street, ' ', 1) AS street_name, 
        count(*)
    FROM evanston311
    GROUP BY street_name
    ORDER BY count DESC
    LIMIT 20;

# Shorten long strings
The description column of evanston311 can be very long. You can get the length of a string with the length() function.

For displaying or quickly reviewing the data, you might want to only display the first few characters. You can use the left() function to get a specified number of characters at the start of each value.

To indicate that more data is available, concatenate '...' to the end of any shortened description. To do this, you can use a CASE WHEN statement to add '...' only when the string length is greater than 50.

Select the first 50 characters of description when description starts with the word "I".

Select the first 50 characters of description with '...' concatenated on the end where the length() of the description is greater than 50 characters. Otherwise just select the description as is.

Select only descriptions that begin with the word 'I' and not the letter 'I'.

For example, you would want to select "I like using SQL!", but would not want to select "In this course we use SQL!".

    -- Select the first 50 chars when length is greater than 50
    SELECT CASE WHEN length(description) > 50
                THEN left(description, 50) || '...'
        -- otherwise just select description
        ELSE description
        END
    FROM evanston311
    -- limit to descriptions that start with the word I
    WHERE description LIKE 'I %'
    ORDER BY description;

# Create an "other" category
If we want to summarize Evanston 311 requests by zip code, it would be useful to group all of the low frequency zip codes together in an "other" category.

Which of the following values, when substituted for ??? in the query, would give the result below?

Query:

SELECT CASE WHEN zipcount < ??? THEN 'other'
       ELSE zip
       END AS zip_recoded,
       sum(zipcount) AS zipsum
  FROM (SELECT zip, count(*) AS zipcount
          FROM evanston311
         GROUP BY zip) AS fullcounts
 GROUP BY zip_recoded
 ORDER BY zipsum DESC;
Result:

zip_recoded    zipsum
60201          19054
60202          11165
null           5528
other          429
60208          255

    SELECT CASE WHEN zipcount < 100 THEN 'other'
        ELSE zip
        END AS zip_recorded,
        sum(zipcount) AS zipsum
    FROM (SELECT zip, count(*) AS zipcount
            FROM evanston311
            GROUP BY zip) AS fullcounts
        GROUP BY zip_recorded


# Group and recode values
There are almost 150 distinct values of evanston311.category. But some of these categories are similar, with the form "Main Category - Details". We can get a better sense of what requests are common if we aggregate by the main category.

To do this, create a temporary table recode mapping distinct category values to new, standardized values. Make the standardized values the part of the category before a dash ('-'). Extract this value with the split_part() function:

split_part(string text, delimiter text, field int)
You'll also need to do some additional cleanup of a few cases that don't fit this pattern.

Then the evanston311 table can be joined to recode to group requests by the new standardized category values.

## Instructions

1. Create recode with a standardized column; use split_part() and then rtrim() to remove any remaining whitespace on the result of split_part().


    -- Fill in the command below with the name of the temp table
    DROP TABLE IF EXISTS recode;

    -- Create and name the temporary table
    CREATE TEMP TABLE recode AS
    -- Write the select query to generate the table with distinct values of category and standardized values
    SELECT DISTINCT category, 
            rtrim(split_part(category, '-', 1)) AS standardized
        -- What table are you selecting the above values from?
        FROM evanston311;
        
    -- Look at a few values before the next step
    SELECT DISTINCT standardized 
    FROM recode
    WHERE standardized LIKE 'Trash%Cart'
        OR standardized LIKE 'Snow%Removal%';

2. UPDATE standardized values LIKE 'Trash%Cart' to 'Trash Cart'.
UPDATE standardized values of 'Snow Removal/Concerns' and 'Snow/Ice/Hazard Removal' to 'Snow Removal'.

    -- Code from previous step
    DROP TABLE IF EXISTS recode;

    CREATE TEMP TABLE recode AS
    SELECT DISTINCT category, 
            rtrim(split_part(category, '-', 1)) AS standardized
        FROM evanston311;

    -- Update to group trash cart values
    UPDATE recode 
    SET standardized='Trash Cart' 
    WHERE standardized LIKE 'Trash%Cart';

    -- Update to group snow removal values
    UPDATE recode 
    SET standardized='Snow Removal' 
    WHERE standardized LIKE 'Snow%';
        
    -- Examine effect of updates
    SELECT DISTINCT standardized 
    FROM recode
    WHERE standardized LIKE 'Trash%Cart'
        OR standardized LIKE 'Snow%Removal%';

3. UPDATE recode by setting standardized values of 'THIS REQUEST IS INACTIVE...Trash Cart', '(DO NOT USE) Water Bill', 'DO NOT USE Trash', and 'NO LONGER IN USE' to 'UNUSED'.

    -- Code from previous step
    DROP TABLE IF EXISTS recode;

    CREATE TEMP TABLE recode AS
    SELECT DISTINCT category, 
            rtrim(split_part(category, '-', 1)) AS standardized
        FROM evanston311;
    
    UPDATE recode SET standardized='Trash Cart' 
    WHERE standardized LIKE 'Trash%Cart';

    UPDATE recode SET standardized='Snow Removal' 
    WHERE standardized LIKE 'Snow%Removal%';

    -- Update to group unused/inactive values
    UPDATE recode 
    SET standardized='UNUSED' 
    WHERE standardized IN ('THIS REQUEST IS INACTIVE...Trash Cart', 
                '(DO NOT USE) Water Bill', 'DO NOT USE Trash',
                'NO LONGER IN USE', 
                'UNUSED');

    -- Examine effect of updates
    SELECT DISTINCT standardized 
    FROM recode
    ORDER BY standardized;

4. Now, join the evanston311 and recode tables to count the number of requests with each of the standardized values
List the most common standardized values first.


    -- Code from previous step
    DROP TABLE IF EXISTS recode;
    CREATE TEMP TABLE recode AS
    SELECT DISTINCT category, 
            rtrim(split_part(category, '-', 1)) AS standardized
    FROM evanston311;
    UPDATE recode SET standardized='Trash Cart' 
    WHERE standardized LIKE 'Trash%Cart';
    UPDATE recode SET standardized='Snow Removal' 
    WHERE standardized LIKE 'Snow%Removal%';
    UPDATE recode SET standardized='UNUSED' 
    WHERE standardized IN ('THIS REQUEST IS INACTIVE...Trash Cart', 
                '(DO NOT USE) Water Bill',
                'DO NOT USE Trash', 'NO LONGER IN USE');

    -- Select the recoded categories and the count of each
    SELECT standardized, count(*)
    -- From the original table and table with recoded values
    FROM evanston311 
        INNER JOIN recode 
        -- What column do they have in common?
        ON recode.category = evanston311.category
    -- What do you need to group by to count?
    GROUP BY standardized
    -- Display the most common val values first
    ORDER BY count DESC;

# Create a table with indicator variables
Determine whether medium and high priority requests in the evanston311 data are more likely to contain requesters' contact information: an email address or phone number.

Emails contain an @.
Phone numbers have the pattern of three characters, dash, three characters, dash, four characters. For example: 555-555-1212.
Use LIKE to match these patterns. Remember % matches any number of characters (even 0), and _ matches a single character. Enclosing a pattern in % (i.e. before and after your pattern) allows you to locate it within other text.

For example, '%___.com%'would allow you to search for a reference to a website with the top-level domain '.com' and at least three characters preceding it.

Create and store indicator variables for email and phone in a temporary table. LIKE produces True or False as a result, but casting a boolean (True or False) as an integer converts True to 1 and False to 0. This makes the values easier to summarize later.

## Instructions 

1. Create a temp table indicators from evanston311 with three columns: id, email, and phone.

Use LIKE comparisons to detect the email and phone patterns that are in the description, and cast the result as an integer with CAST().

Your phone indicator should use a combination of underscores _ and dashes - to represent a standard 10-digit phone number format.
Remember to start and end your patterns with % so that you can locate the pattern within other text!

    -- To clear table if it already exists
    DROP TABLE IF EXISTS indicators;

    -- Create the indicators temp table
    CREATE TEMP TABLE indicators AS
    -- Select id
    SELECT id, 
            -- Create the email indicator (find @)
            CAST (description LIKE '%@%' AS integer) AS email,
            -- Create the phone indicator
            CAST (description LIKE '%___-___-____%' AS integer) AS phone 
        -- What table contains the data? 
        FROM evanston311;

    -- Inspect the contents of the new temp table
    SELECT *
    FROM indicators;

2. Join the indicators table to evanston311, selecting the proportion of reports including an email or phone grouped by priority.
Include adjustments to account for issues arising from integer division.

    -- To clear table if it already exists
    DROP TABLE IF EXISTS indicators;

    -- Create the temp table
    CREATE TEMP TABLE indicators AS
    SELECT id, 
            CAST (description LIKE '%@%' AS integer) AS email,
            CAST (description LIKE '%___-___-____%' AS integer) AS phone 
        FROM evanston311;
    
    -- Select the column you'll group by
    SELECT priority,
        -- Compute the proportion of rows with each indicator
        sum(email)/count(*)::numeric AS email_prop, 
        sum(phone)/count(*)::numeric AS phone_prop
    -- Tables to select from
    FROM evanston311
        LEFT JOIN indicators
        -- Joining condition
        ON evanston311.id=indicators.id
    -- What are you grouping by?
    GROUP BY priority;
