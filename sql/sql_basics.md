Aliasing: Rename columns in your result set for clarity using the AS keyword.
SELECT name AS first_name FROM employees;

DISTINCT: Retrieve unique values from a column to avoid duplicates.
SELECT DISTINCT year_hired FROM employees;

Unique Combinations: Use DISTINCT with multiple fields to get unique field combinations.
SELECT DISTINCT dept_id, year_hired FROM employees;

Views: Save SQL queries as virtual tables with CREATE VIEW for easy reuse.
CREATE VIEW employee_hire_years AS SELECT year_hired FROM employees;


Recall that limiting results is useful when testing code since result sets can have thousands of results. Queries are often written with a LIMIT of just a few records to test out code before selecting thousands of results from the database.

Let's practice with LIMIT!

-- Select the first 10 genres from books using PostgreSQL
SELECT genre FROM books LIMIT 10;
