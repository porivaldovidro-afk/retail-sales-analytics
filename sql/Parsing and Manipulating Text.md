Learn how to manipulate string and text data by transforming case, parsing and truncating text and extracting substrings from larger strings.

## Concatenating strings

In this exercise and the ones that follow, we are going to derive new fields from columns within the customer and film tables of the DVD rental database.

We'll start with the customer table and create a query to return the customers name and email address formatted such that we could use it as a "To" field in an email script or program. This format will look like the following:

Brian Piccolo <bpiccolo@datacamp.com>

**In the first step of the exercise, use the || operator to do the string concatenation and in the second step, use the CONCAT() functions.**

### Instructions 

Concatenate the first_name and last_name columns separated by a single space followed by email surrounded by < and >.

    -- Concatenate the first_name and last_name and email 
    SELECT first_name || ' ' || last_name || ' <' || email || '>' AS full_email 
    FROM customer

Now use the CONCAT() function to do the same operation as the previous step.

    -- Concatenate the first_name and last_name and email
    SELECT CONCAT(first_name, ' ', last_name,  ' <', email, '>') AS full_email 
    FROM customer

## Changing the case of string data

Now you are going to use the film and category tables to create a new field called film_category by concatenating the category name with the film's title. You will also format the result using functions you learned about in the video to transform the case of the fields you are selecting in the query; for example, the INITCAP() function which converts a string to title case.

## Instructions

Convert the film category name to uppercase.
Convert the first letter of each word in the film's title to upper case.
Concatenate the converted category name and film title separated by a colon.
Convert the description column to lowercase.

    SELECT 
    -- Concatenate the category name to coverted to uppercase
    -- to the film title converted to title case
    UPPER(name)  || ': ' || INITCAP(title) AS film_category, 
    -- Convert the description column to lowercase
    LOWER(description) AS description
    FROM 
    film AS f 
    INNER JOIN film_category AS fc 
        ON f.film_id = fc.film_id 
    INNER JOIN category AS c 
        ON fc.category_id = c.category_id;

## Replacing string data

Sometimes you will need to make sure that the data you are extracting does not contain any whitespace. There are many different approaches you can take to cleanse and prepare your data for these situations. A common technique is to replace any whitespace with an underscore.

In this example, we are going to practice finding and replacing whitespace characters in the title column of the film table using the REPLACE() function.


### Instructions

Replace all whitespace with an underscore.

    SELECT 
    -- Replace whitespace in the film title with an underscore
    REPLACE(title, ' ', '_') AS title
    FROM film;

## Determining the length of strings

Determining the number of characters in a string is something that you will use frequently when working with data in a SQL database. Many situations will require you to find the length of a string stored in your database. For example, you may need to limit the number of characters that are displayed in an application or you may need to ensure that a column in your dataset contains values that are all the same length. In this example, we are going to determine the length of the description column in the film table of the DVD Rental database.

### Instructions

Select the title and description columns from the film table.
Find the number of characters in the description column with the alias desc_len.

    SELECT 
    -- Select the title and description columns
    title,
    description,
    -- Determine the length of the description column
    LENGTH(description) AS desc_len
    FROM film;

## Truncating strings

In the previous exercise, you calculated the length of the description column and noticed that the number of characters varied but most of the results were over 75 characters. There will be many times when you need to truncate a text column to a certain length to meet specific criteria for an application. In this exercise, we will practice getting the first 50 characters of the description column.


### Instructions

Select the first 50 characters of the description column with the alias short_desc

    SELECT 
    -- Select the first 50 characters of description
    LEFT(description, 50) AS short_desc
    FROM 
    film AS f; 

## Extracting substrings from text data

In this exercise, you are going to practice how to extract substrings from text columns. The Sakila database contains the address table which stores the street address for all the rental store locations. You need a list of all the street names where the stores are located but the address column also contains the street number. You'll use several functions that you've learned about in the video to manipulate the address column and return only the street address.

### Instructions

Extract only the street address without the street number from the address column.
Use functions to determine the starting and ending position parameters.

    SELECT 
    -- Select only the street name from the address table
    SUBSTRING(address FROM POSITION(' ' IN address)+1 FOR LENGTH(address))
    FROM 
    address;

## Combining functions for string manipulation

In the next example, we are going to break apart the email column from the customer table into three new derived fields. Parsing a single column into multiple columns can be useful when you need to work with certain subsets of data. Email addresses have embedded information stored in them that can be parsed out to derive additional information about our data. For example, we can use the techniques we learned about in the video to determine how many of our customers use an email from a specific domain.

### Instructions
Extract the characters to the left of the @ of the email column in the customer table and alias it as username.
Now use SUBSTRING to extract the characters after the @ of the email column and alias the new derived field as domain.

    SELECT
    -- Extract the characters to the left of the '@'
    LEFT(email, POSITION('@' IN email)-1) AS username,
    -- Extract the characters to the right of the '@'
    SUBSTRING(email FROM POSITION('@' IN email)+1 FOR LENGTH(email)) AS domain
    FROM customer;

## Padding
Padding strings is useful in many real-world situations. Earlier in this course, we learned about string concatenation and how to combine the customer's first and last name separated by a single blank space and also combined the customer's full name with their email address.

The padding functions that we learned about in the video are an alternative approach to do this task. To use this approach, you will need to combine and nest functions to determine the length of a string to produce the desired result. Remember when calculating the length of a string you often need to adjust the integer returned to get the proper length or position of a string.

Let's revisit the string concatenation exercise but use padding functions.

Add a single space to the end or right of the first_name column using a padding function.
Use the || operator to concatenate the padded first_name to the last_name column.

    -- Concatenate the padded first_name and last_name 
    SELECT 
        RPAD(first_name, LENGTH(first_name)+1) || last_name AS full_name
    FROM customer;

Now add a single space to the left or beginning of the last_name column using a different padding function than the first step.
Use the || operator to concatenate the first_name column to the padded last_name.

    -- Concatenate the first_name and last_name 
    SELECT 
        first_name || LPAD(last_name, LENGTH(last_name)+1) AS full_name
    FROM customer; 

Add a single space to the right or end of the first_name column.
Add the characters < to the right or end of last_name column.
Finally, add the characters > to the right or end of the email column.

    -- Concatenate the first_name and last_name 
    SELECT 
        RPAD(first_name, LENGTH(first_name)+1) 
        || RPAD(last_name, LENGTH(last_name)+2, ' <') 
        || RPAD(email, LENGTH(email)+1, '>') AS full_email
    FROM customer; 

## The TRIM function
In this exercise, we are going to revisit and combine a couple of exercises from earlier in this chapter. If you recall, you used the LEFT() function to truncate the description column to 50 characters but saw that some words were cut off and/or had trailing whitespace. We can use trimming functions to eliminate the whitespace at the end of the string after it's been truncated.

    -- Concatenate the uppercase category name and film title
    SELECT 
    CONCAT(UPPER(name), ': ', title) AS film_category, 
    -- Truncate the description remove trailing whitespace
    TRIM(LEFT(description, 50)) AS film_desc
    FROM 
    film AS f 
    INNER JOIN film_category AS fc 
        ON f.film_id = fc.film_id 
    INNER JOIN category AS c 
        ON fc.category_id = c.category_id;
    
## Putting it all together
In this exercise, we are going to use the film and category tables to create a new field called film_category by concatenating the category name with the film's title. You will also practice how to truncate text fields like the film table's description column without cutting off a word.

To accomplish this we will use the REVERSE() function to help determine the position of the last whitespace character in the description before we reach 50 characters. This technique can be used to determine the position of the last character that you want to truncate and ensure that it is less than or equal to 50 characters AND does not cut off a word.

This is an advanced technique but I know you can do it! Let's dive in.

Get the first 50 characters of the description column
Determine the position of the last whitespace character of the truncated description column and subtract it from the number 50 as the second parameter in the first function above.

    SELECT 
    UPPER(c.name) || ': ' || f.title AS film_category, 
    -- Truncate the description without cutting off a word
    LEFT(description, 50 - 
        -- Subtract the position of the first whitespace character
        POSITION(
        ' ' IN REVERSE(LEFT(description, 50))
        )
    ) 
    FROM 
    film AS f 
    INNER JOIN film_category AS fc 
        ON f.film_id = fc.film_id 
    INNER JOIN category AS c 
        ON fc.category_id = c.category_id;

