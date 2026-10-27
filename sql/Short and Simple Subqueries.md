# WHERE are the Subqueries

In this chapter, you will learn about subqueries in the SELECT, FROM, and WHERE clauses. You will gain an understanding of when subqueries are necessary to construct your dataset and where to best include them in your queries.

This lesson dives into the use of simple subqueries in SQL to transform and extract data efficiently.

Key Points:

Subquery Basics: A subquery is a query within another query, often used for data transformation.
Placement: Subqueries can be in SELECT, FROM, WHERE, or GROUP BY clauses.
Purpose: They help compare summarized values, reshape data, and combine data from non-joinable tables.
Example Code:

    SELECT match_id
    FROM match
    WHERE home_goals > (
        SELECT AVG(home_goals) 
        FROM match
    );

Use Case: Find matches where home goals exceed the average.
Practice: Create subqueries in the WHERE clause for filtering.

# Filtering using scalar subqueries

Subqueries are incredibly powerful for performing complex filters and transformations. You can filter data based on single (scalar) values using a subquery in ways you cannot by using WHERE statements or joins. Subqueries can also be used for more advanced manipulation of your dataset. You will likely encounter subqueries in any real-world setting that uses relational databases.

In this exercise, you will generate a list of matches where the total goals scored (for both teams in total) is more than 3 times the average for games in the matches_2013_2014 table, which includes all games played in the 2013/2014 season.

Calculate triple the average home + away goals scored across all matches in a subquery.
Filter the main query for matches where the total goals (home + away goals) exceed the value in the subquery.

    SELECT 
        date,
        home_goal,
        away_goal
    FROM matches_2013_2014
    -- Filter for matches where total goals is greater than 3x the average
    WHERE (home_goal + away_goal) > 
        (SELECT 3 * AVG(home_goal + away_goal)
            FROM matches_2013_2014);

## Filtering using a subquery with a list

Your goal in this exercise is to generate a list of teams that never played a game in their home city. Using a subquery, you will generate a list of unique hometeam_ID values from the unfiltered match table to exclude in the team table's team_api_ID column.

In addition to filtering using a single-value (scalar) subquery, you can create a list of values in a subquery to filter data based on a complex set of conditions. This type of subquery generates a one column reference list for the main query. As long as the values in your list match a column in your main query's table, you don't need to use a join -- even if the list is from a separate table.

Create a subquery in the WHERE clause that retrieves all unique hometeam_id values from the match table.
Select the team_long_name and team_short_name from the team table. Exclude all values from the subquery in the main query.

    SELECT 
        -- Select the team long and short names
        team_long_name,
        team_short_name
    FROM team 
    -- Exclude all values from the subquery
    WHERE team_api_id NOT IN
        (SELECT DISTINCT hometeam_id  FROM match);

## Filtering with more complex subquery conditions

In the previous exercise, you generated a list of teams that have no home matches listed in the soccer database using a subquery in WHERE. Let's do some further exploration in this database by creating a list of teams that scored 8 or more goals in a home match.

In order to do this, you will construct a subquery in the WHERE statement with its own filtering condition.

Create a subquery in WHERE clause that retrieves all hometeam_ID values from match with a home_goal score greater than or equal to 8.
Select the team_long_name and team_short_name from the team table. Include all values from the subquery in the main query.

    SELECT
        -- Select the team long and short names
        team_long_name,
        team_short_name
    FROM team
    -- Subquery to filter for teams with 8 or more home goals
    WHERE team_api_id IN
        (SELECT hometeam_id 
        FROM match
        WHERE home_goal >= 8);

# Subqueries in FROM

In this lesson, you'll dive into using subqueries within the FROM statement to transform and prepare data for analysis. Subqueries in FROM allow you to:

* Restructure data for complex queries
* Pre-filter data before calculations
* Calculate aggregates of aggregate information

For example, to find the top 3 teams with the highest average home goals in the 2011/2012 season, you can use a subquery. Here's a simplified version of the process:

    SELECT team, home_avg
    FROM (
    SELECT t.long_name AS team, AVG(m.home_goal) AS home_avg
    FROM team t
    LEFT JOIN match m ON t.team_id = m.hometeam_id
    WHERE m.season = '2011/2012'
    GROUP BY t.long_name
    ) AS subquery
    ORDER BY home_avg DESC
    LIMIT 3;

Remember to use aliases and ensure proper JOINs when working with subqueries. Now, it's your turn to practice!


## Joining Subqueries in FROM

The match table in the European Soccer Database does not contain country or team names. You can get this information by joining it to the country table, and use this to aggregate information, such as the number of matches played in each country.

If you're interested in filtering data from one of these tables, you can also create a subquery from one of the tables, and then join it to an existing table in the database. A subquery in FROM is an effective way of answering detailed questions that requires filtering or transforming data before including it in your final results.

Your goal in this exercise is to generate a subquery using the match table, and then join that subquery to the country table to calculate information about matches with 10 or more goals in total!

Create the subquery to be used in the next step, which selects the country ID and match ID (id) from the match table.
Filter the query for matches with greater than or equal to 10 goals.

    SELECT 
        -- Select the country ID and match ID
        country_id, 
        id 
    FROM match
    -- Filter for matches with 10 or more goals in total
    WHERE (home_goal + away_goal) >= 10;

Construct a subquery that selects only matches with 10 or more total goals.
INNER JOIN the subquery onto country in the main query.
Select name from country and COUNT() the id column from match

    SELECT
        -- Select country name and the count match IDs
        c.name AS country_name,
        COUNT(sub.id) AS matches
    FROM country AS c
    -- Inner join the subquery onto country
    -- Select the country id and match id columns
    INNER JOIN (SELECT country_id, id 
            FROM match
            -- Filter the subquery by matches with 10+ goals
            WHERE (home_goal + away_goal) >= 10) AS sub
    ON c.id = sub.country_id
    GROUP BY country_name;

## Building on Subqueries in FROM

In the previous exercise, you found that England, Netherlands, Germany and Spain were the only countries that had matches in the database where 10 or more goals were scored overall. Let's find out some more details about those matches -- when they were played, during which seasons, and how many of the goals were home versus away goals.

You'll notice that in this exercise, the table alias is excluded for every column selected in the main query. This is because the main query is extracting data from the subquery, which is treated as a single table.

Complete the subquery inside the FROM clause. Select the country name from the country table, along with the date, the home_goal, the away_goal, and the total_goals columns from the match table.
Create a column in the subquery that adds home and away goals, called total_goals. This will be used to filter the main query.
Select the country, date, home_goal, and away_goal in the main query.
Filter the main query for games with 10 or more total goals.

    SELECT
        -- Select country, date, home, and away goals from the subquery
        country,
        date,
        home_goal,
        away_goal
    FROM 
        -- Select country name, date, home_goal, away_goal, and total goals in the subquery
        (SELECT name AS country, 
                m.date, 
                home_goal, 
                m.away_goal,
            (home_goal + m.away_goal) AS total_goals
        FROM match AS m
        LEFT JOIN country AS c
        ON m.country_id = c.id) AS subq
    -- Filter by total goals scored in the main query
    WHERE total_goals >= 10;

# Subqueries in SELECT

In this lesson, you'll explore the use of subqueries in SELECT statements to enhance your SQL queries. Key points include:

Subqueries in SELECT return a single, aggregate value.
Useful for incorporating summary values into detailed datasets.
Ideal for complex calculations, like comparing individual scores to averages.
Ensure subqueries return a single value to avoid errors.
Correctly place filters in both main and subqueries.
Here's a quick example:

    SELECT 
        match_id, 
        goals_scored - (SELECT AVG(goals_scored) FROM matches) AS deviation_from_avg
    FROM 
        matches
    WHERE 
        season = '2011/2012';

This query calculates how much each match's goals deviate from the average.

## Add a subquery to the SELECT clause

Subqueries in SELECT statements generate a single value that allow you to pass an aggregate value down a data frame. This is useful for performing calculations on data within your database.

In the following exercise, you will construct a query that calculates the average number of goals per match in each country's league.

In the subquery, Select the average total goals by adding home_goal and away_goal, then
Filter the results so that only the average of goals in the '2013/2014' season is calculated.
In the main query, select the average total goals by adding home_goal and away_goal. This calculates the average goals for each league, then
Filter the results in the main query the same way you filtered the subquery. GROUP the query BY the league name.

    SELECT 
        l.name AS league,
        -- Round the average of the league's total goals
        ROUND(AVG(m.home_goal + m.away_goal), 2) AS avg_goals,
        -- Select and round the average total goals for the season
        (SELECT ROUND(AVG(home_goal + away_goal), 2) 
        FROM match
        WHERE season = '2013/2014') AS overall_avg
    FROM league AS l
    LEFT JOIN match AS m
    ON l.country_id = m.country_id
    -- Filter for the 2013/2014 season
    WHERE season = '2013/2014'
    GROUP BY name;

## Subqueries in Select for Calculations

Subqueries in SELECT are a useful way to create calculated columns in a query. A subquery in SELECT can be treated as a single numeric value to use in your calculations. When writing queries in SELECT, it's important to remember that filtering the main query does not filter the subquery -- and vice versa.

In the previous exercise, you created a column to compare each league's average total goals to the overall average goals in the 2013/2014 season. In this exercise, you will add a column that directly compares these values by subtracting the overall average from the subquery.

Select the average goals scored in a match for each league in the main query.
Select the average goals scored in a match overall for the '2013/2014' season in the subquery.
Subtract the subquery from the average number of goals calculated for each league.
Filter the main query so that only games from the '2013/2014' season are included.

    SELECT
        -- Select the league name and average goals scored
        l.name AS league,
        ROUND(AVG(m.home_goal + m.away_goal),2) AS avg_goals,
        -- Subtract the overall average from the league average
        ROUND(AVG(m.home_goal + m.away_goal) -
            (SELECT AVG(home_goal + away_goal)
            FROM match 
            WHERE season = '2013/2014'),2) AS diff
    FROM league AS l
    LEFT JOIN match AS m
    ON l.country_id = m.country_id
    -- Only include 2013/2014 results
    WHERE season = '2013/2014'
    GROUP BY l.name;

# Subqueries everywhere! And best pratices!

Dive into the world of SQL subqueries and enhance your query-building skills. This lesson focuses on using multiple subqueries effectively and maintaining readability in your SQL code.

Key Learning Points:

Utilize subqueries in SELECT, FROM, and WHERE clauses.
Importance of query formatting for readability.
Annotate queries with comments for clarity.
Indent subqueries and complex statements for better understanding.
Evaluate the necessity of subqueries to optimize performance.
Example Code:

    SELECT player_name, 
        (SELECT AVG(goals) FROM matches WHERE season = '2013/2014') AS avg_goals
    FROM players
    WHERE team_id IN (SELECT team_id FROM teams WHERE league = 'Premier League');

Remember, clear and efficient queries lead to accurate results. Happy querying!

## ALL the subqueries EVERYWHERE

In soccer leagues, games are played at different stages. Winning teams progress from one stage to the next, until they reach the final stage. In each stage, the stakes become higher than the previous one. The match table includes data about the different stages that each match took place in.

Across the next 3 exercises, you will build one query with a subquery in each of the SELECT, FROM, and WHERE clauses. Does the average number of goals scored change as the stakes get higher from one stage to the next?

The query repeats one subquery so you can practice each placement. Every copy reads the match table again, so in your own work you'd calculate the overall average once, as Chapter 4 shows with a window function.

Extract the average number of total goals in a SELECT subquery.
Calculate the average total goals for the specific stage in the main query.
Filter both subqueries and the main query so that only data from the 2012/2013 season is included.
GROUP the query by the m.stage column.

    SELECT 
        -- Select the stage and average goals for each stage
        m.stage,
        ROUND(AVG(m.home_goal + m.away_goal),2) AS avg_goals,
        -- Select the average overall goals for the 2012/2013 season
        ROUND((SELECT AVG(home_goal + away_goal) 
            FROM match 
            WHERE season = '2012/2013'),2) AS overall
    FROM match AS m
    -- Filter for the 2012/2013 season
    WHERE season = '2012/2013'
    -- Group by stage
    GROUP BY m.stage;

## Add a subquery in FROM

In the previous exercise, you created a data set listing the average home and away goals in each match stage of the 2012/2013 match season.

In this next step, you will turn the main query into a subquery to extract a list of stages where the average total goals in a stage is higher than the overall average for total goals in a match.

Calculate the average total goals from the match table for each stage in the FROM clause subquery.
Add a subquery to the WHERE clause that calculates the overall average total goals.
Filter the main query for stages where the average total goals is higher than the overall average.
Select the stage and avg_goals columns from the s subquery into the main query.

    SELECT 
        -- Select the stage and average goals from the subquery
        stage,
        ROUND(s.avg_goals,2) AS avg_goals
    FROM 
        -- Select the stage and average goals in 2012/2013
        (SELECT
            stage,
            AVG(home_goal + away_goal) AS avg_goals
        FROM match
        WHERE season = '2012/2013'
        GROUP BY stage) AS s
    WHERE 
        -- Filter the main query using the subquery
        s.avg_goals > (SELECT AVG(home_goal + away_goal) 
                        FROM match WHERE season = '2012/2013');

# Add a subquery in SELECT

In the previous exercise, you added a subquery to the FROM statement and selected the stages where the number of average goals in a stage exceeded the overall average number of goals in the 2012/2013 match season. In this final step, you will add a subquery in SELECT to compare the average number of goals scored in each stage to the total.

Create a subquery in SELECT that yields the average total goals scored in the 2012/2013 season and stores it in a column named overall_avg
Create a subquery in FROM that calculates the average total goals scored in each stage during the 2012/2013 season.
Filter the main query for stages where the average total goals exceeds the overall average in 2012/2013.

    SELECT 
        -- Select the stage and average goals from s
        stage,
        ROUND(avg_goals,2) AS avg_goal,
        -- Select the overall average for 2012/2013
        (SELECT AVG(home_goal + away_goal) FROM match WHERE season = '2012/2013') AS overall_avg
    FROM 
        -- Select the stage and average goals in 2012/2013 from match
        (SELECT
            stage,
            AVG(home_goal + away_goal) AS avg_goals
        FROM match
        WHERE season = '2012/2013'
        GROUP BY stage) AS s
    WHERE 
        -- Filter the main query using the subquery
        s.avg_goals > (SELECT AVG(home_goal + away_goal) 
                        FROM match WHERE season = '2012/2013');
