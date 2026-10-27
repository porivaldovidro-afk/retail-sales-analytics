In this chapter, you will learn how to use nested and correlated subqueries to extract more complex data from a relational database. You will also learn about common table expressions and how to best construct queries using multiple common table expressions.

# Correlated subqueries

In this lesson, you’ll dive into correlated subqueries, a powerful tool in SQL that uses values from the outer query to generate results. Unlike simple subqueries, correlated subqueries are re-executed for each row, making them useful for advanced data calculations but potentially slower.

Key Points:

Correlated subqueries depend on the outer query and are evaluated for each row.
Useful for advanced joining, filtering, and evaluating data.
Can replace joins in certain queries.
Example Code:

SELECT c.name, 
       (SELECT AVG(goals) 
        FROM match 
        WHERE match.country_id = c.id) AS avg_goals
FROM country c;

Practice using these in your exercises to master their utility!

## Getting started with correlated subqueries

Correlated subqueries are subqueries that reference one or more columns in the main query. Correlated subqueries depend on information in the main query to run, and thus, cannot be executed on their own.

Correlated subqueries are evaluated in SQL once per row of data retrieved—a process that takes a lot more computing power and time than a simple subquery.

In this exercise, you will practice using correlated subqueries to examine matches with scores that are extreme outliers for each country—above 3 times the average score!

Start the subquery in WHERE by taking the average/mean of the home and away goals.
Complete the subquery column references, so that country_id is matched in the main and sub tables.

    SELECT 
        main.country_id,
        main.date,
        main.home_goal,
        main.away_goal
    FROM match AS main
    WHERE 
        -- Filter the main query by the subquery
        (home_goal + away_goal) > 
            (SELECT AVG((sub.home_goal  + sub.away_goal) * 3)
            FROM match AS sub
            -- Join the main query to the subquery with country_id in WHERE
            WHERE main.country_id = sub.country_id);

## Correlated subquery with multiple conditions

Correlated subqueries are useful for matching data across multiple columns. In the previous exercise, you generated a list of matches with extremely high scores for each country. In this exercise, you're going to add an additional column for matching to answer the question—what was the highest scoring match for each country, in each season?

*Note: this query may take a while to load.

Complete the subquery to select the matches with the highest number of total goals.
Match the subquery to the main query using the country_id and season columns in both tables.

    SELECT 
        main.country_id,
        main.date,
        main.home_goal,
        main.away_goal
    FROM match AS main
    WHERE 
        -- Filter for matches with the maximum number of total goals scored
        (home_goal + away_goal) = 
            (SELECT MAX(sub.home_goal + sub.away_goal)
            FROM match AS sub
            -- Join the main query to the subquery in WHERE
            WHERE main.country_id = sub.country_id
                AND main.season = sub.season);

# Nested subquries

In this lesson, you'll dive into nested subqueries, a powerful tool for complex data queries. Learn how to layer subqueries to transform and filter data effectively.

Understand nested subqueries: subqueries within subqueries.
Learn to calculate monthly goal differences using nested subqueries.
Explore correlated and uncorrelated subqueries.
Here's a sample query to calculate the average goals scored per month:

    SELECT month, SUM(goals) - (SELECT AVG(monthly_goals) FROM (SELECT EXTRACT(MONTH FROM date) AS month, SUM(goals) AS monthly_goals FROM matches GROUP BY month) AS subquery) AS goal_difference
    FROM matches
    GROUP BY month;

Practice creating your own nested subqueries to deepen your understanding!


## Getting started with nested subqueries

Time to take your subqueries up a gear! You only have time to watch so much soccer, and want to know if the England Premier League is exciting as people say.

You'll compare the maximum number of goals scored in a single match across each season against the maximum for the England Premier League.

Complete a nested subquery to select the country_id for 'England Premier League' from league, which will be used to filter the parent subquery.

    SELECT 
        season,
        MAX(home_goal + away_goal) AS max_goals,
        (SELECT MAX(home_goal + away_goal) 
        FROM match 
        WHERE season = main.season
        -- Subquery to get the max goals in an 'England Premier League' match for the same season
        AND country_id IN (1729)
        ) AS pl_max_goals
    FROM match AS main
    GROUP BY season;

## Nest a subquery in FROM

What's the average number of matches per season where a team scored 5 or more goals? How does this differ by country?

Let's use a nested, correlated subquery to perform this operation. In the real world, you will probably find that nesting multiple subqueries is a task you don't have to perform often. In some cases, however, you may find yourself struggling to properly group by the column you want, or to calculate information requiring multiple mathematical transformations (i.e., an AVG of a COUNT).

Nesting subqueries and performing your transformations one step at a time, adding it to a subquery, and then performing the next set of transformations is often the easiest way to yield accurate information about your data. Let's get to it!

Generate a list of matches where at least one team scored 5 or more goals.


    -- Select matches where a team scored 5+ goals
    SELECT
        country_id,
        season,
        id
    FROM match
    WHERE home_goal >= 5 OR away_goal >= 5;

Turn the query from the previous step into a subquery in the FROM statement.
COUNT the match table ids generated in the previous step.

    -- Count match ids
    SELECT
        country_id,
        season,
        COUNT(id) AS matches
    -- Set up and alias the subquery
    FROM (
        SELECT
            country_id,
            season,
            id
        FROM match
        WHERE home_goal >= 5 OR away_goal >= 5)
        AS subquery
    GROUP BY country_id, season;

Finally, declare the same query from step 2 as a subquery in FROM with the alias outer_s.
Left join it to the country table using the outer query's country_id column.
Calculate an AVG of high scoring matches per country in the main query.

    SELECT
        c.name AS country,
        -- Calculate the average matches per season
        AVG(outer_s.matches) AS avg_seasonal_high_scores
    FROM country AS c
    -- Left join outer_s to country
    LEFT JOIN (
    SELECT country_id, season,
            COUNT(id) AS matches
    FROM (
        SELECT country_id, season, id
        FROM match
        WHERE home_goal >= 5 OR away_goal >= 5) AS inner_s
    -- Close parentheses and alias the subquery
    GROUP BY country_id, season) AS outer_s
    ON c.id = outer_s.country_id
    GROUP BY country;

# Common Table Expressions

In this lesson, you'll learn how to enhance the readability and efficiency of SQL queries using Common Table Expressions (CTEs). CTEs are a powerful tool that allows you to define subqueries outside the main query, making complex queries easier to manage.

Define a CTE using the WITH statement
Improve query performance by running CTEs once
Organize complex queries with multiple CTEs
Use CTEs for recursive operations
Here's a simple example of a CTE:

    WITH s AS (
    SELECT country_id, match_id
    FROM matches
    WHERE total_goals >= 10
    )
    SELECT country.name, COUNT(s.match_id)
    FROM s
    JOIN country ON s.country_id = country.id
    GROUP BY country.name;

Practice using CTEs in your exercises from chapter 2.

## Clean up with CTEs

In chapter 2, you generated a list of countries and the number of matches in each country with more than 10 total goals. The query in that exercise utilized a subquery in the FROM statement in order to filter the matches before counting them in the main query. Below is the query you created:

    SELECT
    c.name AS country,
    COUNT(sub.id) AS matches
    FROM country AS c
    INNER JOIN (
    SELECT country_id, id 
    FROM match
    WHERE (home_goal + away_goal) >= 10) AS sub
    ON c.id = sub.country_id
    GROUP BY country;

You can list one (or more) subqueries as common table expressions (CTEs) by declaring them ahead of your main query, which is an excellent tool for organizing information and placing it in a logical order.

In this exercise, let's rewrite a similar query using a CTE.

Complete the syntax to declare your CTE.
Select the country_id and match id from the match table in your CTE.
LEFT JOIN the CTE to the league table using country_id.

    -- Set up your CTE
    WITH match_list AS (
        SELECT 
            country_id, 
            id
        FROM match
        WHERE (home_goal + away_goal) >= 10)
    -- Select league name and count of matches from the CTE
    SELECT
        l.name AS league,
        COUNT(match_list.id) AS matches
    FROM league AS l
    -- Join the CTE to the league table using country_id
    LEFT JOIN match_list ON l.id = match_list.country_id
    GROUP BY l.name;

## Organizing with CTEs

Previously, you modified a query based on a statement you completed in chapter 2 using common table expressions.

This time, let's expand on the exercise by looking at details about matches with very high scores using CTEs. Just like a subquery in FROM, you can join tables inside a CTE.

Declare your CTE, where you create a list of all matches with the league name.
Select the league, date, home, and away goals from the CTE.
Filter the main query for matches with 10 or more goals.

    -- Set up your CTE
    WITH match_list AS (
    -- Select the league name, date, home, and away goals
        SELECT 
            l.name AS league, 
            date, 
            m.home_goal, 
            m.away_goal,
        (m.home_goal + m.away_goal) AS total_goals
        FROM match AS m
        LEFT JOIN league as l ON m.country_id = l.id)
    -- Select the league, date, home, and away goals from the CTE
    SELECT league, date, home_goal, away_goal
    FROM match_list
    -- Filter by total goals
    WHERE total_goals >= 10;

## CTEs with nested subqueries

If you find yourself listing multiple subqueries in the FROM clause with nested statement, your query will likely become long, complex, and difficult to read.

Since many queries are written with the intention of being saved and re-run in the future, proper organization is key to a seamless workflow. Arranging subqueries as CTEs will save you time, space, and confusion in the long run!

Declare a CTE that calculates the total goals from matches in August of the '2013/2014' season.
Filter the list on the inner subquery to only select matches in August of the '2013/2014' season.
LEFT JOIN the CTE onto the league table using country_id from the match_list CTE.

Declare a CTE that calculates the total goals from matches in August of the '2013/2014' season.
Filter the list on the inner subquery to only select matches in August of the '2013/2014' season.
LEFT JOIN the CTE onto the league table using country_id from the match_list CTE.

    -- Set up your CTE
    WITH match_list AS (
        SELECT 
            country_id,
        (home_goal + away_goal) AS goals
        FROM match
        -- Create a list of match IDs to filter data in the CTE
        WHERE id IN (
        SELECT id
        FROM match
        WHERE season = '2013/2014' AND EXTRACT(MONTH FROM date) = 8))
    -- Select the league name and average of goals in the CTE
    SELECT 
        l.name,
        AVG(match_list.goals)
    FROM league AS l
    -- Join the CTE onto the league table using country_id
    LEFT JOIN match_list ON l.id = match_list.country_id
    GROUP BY l.name;

# Deciding on techniques to use

In this lesson, we dive into the nuances of joins, subqueries, and common table expressions (CTEs) in SQL. Each technique has its strengths and specific use cases:

Joins: Combine data from multiple tables, essential for databases with more than one table.
Correlated Subqueries: Match data across columns, useful for tasks like identifying supervisors.
Multiple/Nested Subqueries: Handle complex queries requiring multi-step transformations.
CTEs: Organize and simplify complex queries, ideal for comparing diverse data sets.
Here's a quick example of a CTE:

    WITH TeamPerformance AS (
        SELECT team_id, AVG(score) AS avg_score
        FROM matches
        GROUP BY team_id
    )
    SELECT * FROM TeamPerformance;

Experiment with these techniques to find what works best for your database needs.

## Get team names with a subquery

Let's solve a problem we've encountered a few times in this course so far -- How do you get both the home and away team names into one final query result?

Out of the 4 techniques we just discussed, this can be performed using subqueries, correlated subqueries, and CTEs. Let's practice creating similar result sets using each of these 3 methods over the next 3 exercises, starting with subqueries in FROM.

Warning: if your code times out, you probably made a mistake in the JOIN and tried to join on the wrong fields, which caused the table to be too big! Read the provided code and comments carefully, and check your ON conditions!

Create a query that left joins team to match in order to get the identity of the home team. This becomes the subquery in the next step.

    SELECT 
        m.id, 
        t.team_long_name AS hometeam
    -- Left join match and team
    FROM match AS m
    LEFT JOIN team as t
    ON m.hometeam_id = team_api_id;

Add a second subquery to the FROM statement to get the away team name, changing only the hometeam_id. Left join both subqueries to the match table on the id column.

    SELECT
        m.date,
        -- Get the home team name and away team name from the subqueries
        home.hometeam_name,
        away.awayteam_name,
        m.home_goal,
        m.away_goal
    FROM match AS m

    -- Join the home subquery to the match table
    LEFT JOIN (
    SELECT match.id, team.team_long_name AS hometeam_name
    FROM match
    LEFT JOIN team
    ON match.hometeam_id = team.team_api_id) AS home
    ON home.id = m.id

    -- Join the away subquery to the match table
    LEFT JOIN (
    SELECT match.id, team.team_long_name AS awayteam_name
    FROM match
    LEFT JOIN team
    -- Get the away team ID in the subquery
    ON match.awayteam_id = team.team_api_id) AS away
    ON away.id = m.id;

## Get team names with correlated subqueries

Let's solve the same problem using correlated subqueries -- How do you get both the home and away team names into one final query result?

This can easily be performed using correlated subqueries. But how might that impact the performance of your query? Complete the following steps and let's find out!

Please note that your query will run more slowly than the previous exercise!

Using a correlated subquery in the SELECT statement, match the team_api_id column from team to the hometeam_id from match.

    SELECT
        m.date,
    (SELECT team_long_name
        FROM team AS t
        -- Connect the team's team_api_id to the match's hometeam_id
        WHERE t.team_api_id = m.hometeam_id) AS hometeam
    FROM match AS m;

Create a second correlated subquery in SELECT, yielding the away team's name.
Select the home_goal and away_goal columns from match in the main query.

    SELECT
        m.date,
        (SELECT team_long_name
        FROM team AS t
        WHERE t.team_api_id = m.hometeam_id) AS hometeam,
        -- Connect the team to the match table
        (SELECT team_long_name
        FROM team AS t
        WHERE t.team_api_id = m.awayteam_id) AS awayteam,
        -- Select home_goal and away_goal
        m.home_goal,
        m.away_goal
    FROM match AS m;

## Get team names with CTEs

You've now explored two methods for answering the question, How do you get both the home and away team names into one final query result?

Let's explore the final method - common table expressions. Common table expressions are similar to the subquery method for generating results, mainly differing in syntax and the order in which information is processed.

Select id from match and team_long_name from team. Join these two tables together on hometeam_id in match and team_api_id in team.

    SELECT 
        -- Select match id and team long name
        m.id, 
        t.team_long_name AS hometeam
    FROM match AS m
    -- Join team to match using team_api_id and hometeam_id
    LEFT JOIN team AS t 
    ON t.team_api_id = m.hometeam_id;

Declare the query from the previous step as a common table expression. SELECT everything from the CTE into the main query. Your results will not change at this step!

    -- Declare the home CTE
    WITH home AS (
        SELECT m.id, t.team_long_name AS hometeam
        FROM match AS m
        LEFT JOIN team AS t 
        ON m.hometeam_id = t.team_api_id)
    -- Select everything from home
    SELECT *
    FROM home;

Let's declare the second CTE, away. Join it to the first CTE on the id column.
The date, home_goal, and away_goal columns have been added to the CTEs. SELECT them into the main query.

    WITH home AS (
    SELECT m.id, m.date, 
            t.team_long_name AS hometeam, m.home_goal
    FROM match AS m
    LEFT JOIN team AS t 
    ON m.hometeam_id = t.team_api_id),
    -- Declare and set up the away CTE
    away AS (
    SELECT m.id, m.date, 
            t.team_long_name AS awayteam, m.away_goal
    FROM match AS m
    LEFT JOIN team AS t 
    ON m.awayteam_id = t.team_api_id)
    -- Select date, home_goal, and away_goal
    SELECT 
        home.date,
        home.hometeam,
        away.awayteam,
        home.home_goal,
        away.away_goal
    -- Join away and home on the id column
    FROM home
    INNER JOIN away
    ON home.id = away.id;

Which technique to use?
The previous three exercises demonstrated that, in many cases, you can use multiple techniques in SQL to answer the same question.

Based on what you learned, which of the following statements is false regarding differences in the use and performance of multiple/nested subqueries, correlated subqueries, and common table expressions?

