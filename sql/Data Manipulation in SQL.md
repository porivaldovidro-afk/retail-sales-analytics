# We'll take the CASE

In this chapter, you will learn how to use the CASE WHEN statement to create categorical variables, aggregate data into a single column with multiple filtering conditions, and calculate counts and percentages.

In this lesson, you'll dive into intermediate SQL to enhance your data manipulation skills. You'll learn to:

Shape and transform data using CASE statements
Use simple and correlated subqueries
Apply window functions for advanced data analysis
We'll work with the European Soccer Database, focusing on analyzing match outcomes. A key tool is the CASE statement, which helps categorize data efficiently.

Here's a quick example:

    SELECT
    CASE
        WHEN home_goal > away_goal THEN 'Home Win'
        WHEN home_goal < away_goal THEN 'Away Win'
        ELSE 'Tie'
    END AS match_outcome
    FROM Match
    WHERE season = '2013/2014';

Get ready to categorize and analyze data effectively!

## Basic CASE statements

What is your favorite team?

The European Soccer Database contains data about 12,800 matches from 11 countries played between 2011-2015! Throughout this course, you will be shown filtered versions of the tables in this database in order to better explore their contents.

In this exercise, you will identify matches played between FC Schalke 04 and FC Bayern Munich. There are 2 teams identified in each match in the hometeam_id and awayteam_id columns, available to you in the filtered matches_germany table. ID can join to the team_api_id column in the teams_germany table, but you cannot perform a join on both at the same time.

However, you can perform this operation using a CASE statement once you've identified the team_api_id associated with each team!

Create a CASE statement that identifies whether a match in Germany included FC Bayern Munich, FC Schalke 04, or Other as the home team.

    -- Identify the home team as Bayern Munich, Schalke 04, or Other
    SELECT 
        CASE WHEN hometeam_id = 10189 THEN 'FC Schalke 04'
            WHEN hometeam_id = 9823 THEN 'FC Bayern Munich'
            ELSE 'Other' END AS home_team,
        COUNT(id) AS total_matches
    FROM matches_germany
    GROUP BY home_team;

## CASE statements comparing column values

Barcelona is considered one of the strongest teams in Spain's soccer league.

In this exercise, you will be creating a list of matches in the 2011/2012 season where Barcelona was the home team. You will do this using a CASE statement that compares the values of two columns to create a new group -- wins, losses, and ties.

In 3 steps, you will build a query that identifies a match's winner, identifies the identity of the opponent, and finally filters for Barcelona as the home team. Completing a query in this order will allow you to watch your results take shape with each new piece of information.

The matches_spain table currently contains Barcelona's matches from the 2011/2012 season, and has two key columns, hometeam_id and awayteam_id, that can be joined with the teams_spain table. However, you can only join teams_spain to one column at a time.

Create a CASE statement to identify matches as home wins, home losses, or else ties.

    SELECT 
        date,
        -- Identify home wins, losses, or ties
        CASE WHEN home_goal > away_goal THEN 'Home win!'
            WHEN home_goal < away_goal THEN 'Home loss :(' 
            ELSE 'Tie' END AS outcome
    FROM matches_spain;

## CASE statements comparing two column values part 2

Similar to the previous exercise, you will construct a query to determine the outcome of Barcelona's matches where they played as the away team. You will learn how to combine these two queries in chapters 2 and 3.

Did their performance differ from the matches where they were the home team?

Complete the CASE statement to identify Barcelona's away team games as wins, losses, or ties.
Left join the teams_spain table team_api_id column on the matches_spain table hometeam_id column. This retrieves the identity of the home team opponent.
Filter the query to only include matches where Barcelona (awayteam_id = 8634) was the away team.

    -- Select matches where Barcelona was the away team
    SELECT  
        m.date,
        t.team_long_name AS opponent,
        CASE WHEN home_goal < away_goal THEN 'Barcelona win!'
            WHEN home_goal > away_goal THEN 'Barcelona loss :(' 
            ELSE 'Tie' END AS outcome
    FROM matches_spain AS m
    LEFT JOIN teams_spain AS t 
    ON m.hometeam_id = t.team_api_id
    -- Filter for Barcelona
    WHERE m.awayteam_id = 8634;

# In CASE things get more complex

In this lesson, you'll dive deeper into using CASE statements for more complex logical tests. You'll explore how to categorize match outcomes based on goals and identify specific team wins using multiple conditions.

Learn to use AND in WHEN clauses for multiple logical tests
Understand the importance of the ELSE clause and how it categorizes data
Discover how to filter queries using CASE statements in the WHERE clause
Here's a sample query to identify Chelsea's wins:

    SELECT 
    CASE 
        WHEN hometeam_id = 8455 AND home_goals > away_goals THEN 'Chelsea home win!'
        WHEN awayteam_id = 8455 AND away_goals > home_goals THEN 'Chelsea away win!'
        ELSE 'Loss or tie :('
    END AS match_outcome
    FROM matches
    WHERE hometeam_id = 8455 OR awayteam_id = 8455;

This query helps you focus on Chelsea's performance, filtering out irrelevant matches.

## In CASE of rivalry

Barcelona and Real Madrid have been rival teams for more than 80 years. Matches between these two teams are given the name El Clásico (The Classic). In this exercise, you will query a list of matches played between these two rivals, where Barcelona is the home team, categorizing as a home or away win depending on multiple conditions.

Construct the CASE statement identifying who won each match.
Fill in the operators to identify Barcelona or Real Madrid as the winner.

    SELECT 
        date,
        CASE WHEN hometeam_id = 8634 THEN 'FC Barcelona' 
            ELSE 'Real Madrid CF' END as home,
        CASE WHEN awayteam_id = 8634 THEN 'FC Barcelona' 
            ELSE 'Real Madrid CF' END as away,
        -- Identify possible home match outcomes
        CASE WHEN home_goal > away_goal AND hometeam_id = 8634 THEN 'Barcelona win!'
            WHEN home_goal < away_goal AND awayteam_id = 8633 THEN 'Real Madrid win!'
            ELSE 'Tie!' END AS outcome
    FROM matches_spain
    WHERE hometeam_id = 8634 AND awayteam_id = 8633;

## Filtering your CASE statement

Let's generate a list of matches won by Italy's Bologna team! There are quite a few additional teams in the two tables, so a key part of generating a usable query will be using your CASE statement as a filter in the WHERE clause.

CASE statements allow you to categorize data that you're interested in -- and exclude data you're not interested in. In order to do this, you can use a CASE statement as a filter in the WHERE statement to remove output you don't want to see.

Here is how you might set that up:

    SELECT *
    FROM table
    WHERE 
        CASE WHEN a > 5 THEN 'Keep'
            WHEN a <= 5 THEN 'Exclude' END = 'Keep';

In essence, you can use the CASE statement as a filtering column like any other column in your database. The only difference is that you don't alias the statement in WHERE.

Use the CASE statement in the WHERE clause to filter where hometeam_id is 9857 and home goals exceed away goals.
Use the CASE statement in the WHERE clause to filter where awayteam_id is 9857 and away goals exceed home goals.
End the CASE statement by excluding games that Bologna did not win.

se the CASE statement in the WHERE clause to filter where hometeam_id is 9857 and home goals exceed away goals.
Use the CASE statement in the WHERE clause to filter where awayteam_id is 9857 and away goals exceed home goals.
End the CASE statement by excluding games that Bologna did not win.

    SELECT 
        season,
        date,
        home_goal,
        away_goal
    FROM matches_italy
    WHERE 
        -- Find games where home_goal is more than away_goal
        CASE WHEN hometeam_id = 9857 AND home_goal > away_goal THEN 'Bologna Win'
            -- Find games where away_goal is more than home_goal
            WHEN awayteam_id = 9857 AND away_goal > home_goal THEN 'Bologna Win' 
            -- Exclude games not won by Bologna
            END IS NOT NULL;

# CASE WHEN with aggregate functions

In this lesson, you'll explore how to use CASE statements with aggregate functions in SQL to analyze data. You'll learn to:

Create columns for categorizing data and filtering with CASE in the WHERE clause.
Aggregate data using CASE within functions like COUNT, SUM, and AVG.
Count Liverpool's home and away wins using CASE with COUNT.
Calculate total goals using CASE with SUM.
Determine average goals and win percentages using CASE with AVG.
Here's a quick example of using CASE with COUNT:

    SELECT season,
        COUNT(CASE WHEN hometeam_id = 'Liverpool' AND home_goals > away_goals THEN match_id END) AS home_wins
    FROM matches
    GROUP BY season;

Now, dive into the exercises to practice these concepts!


## COUNT using CASE WHEN

Do the number of soccer matches played in a given European country differ across seasons? We will use the European Soccer Database to answer this question.

You will examine the number of matches played in 3 seasons within each country listed in the database. This is much easier to explore with each season's matches in separate columns. Using the country and unfiltered match table, you will count the number of matches played in each country during the 2012/2013 and 2013/2014 seasons.

Create a CASE WHEN statement counting the matches played in the '2012/2013' season.
Create a CASE WHEN statement counting the matches played in the'2013/2014' season, aliasing as matches_2013_2014.

    SELECT 
        c.name AS country,
        -- Count matches in 2012/2013
        COUNT(CASE WHEN m.season = '2012/2013' THEN m.id END) AS matches_2012_2013,
        -- Count matches in 2013/2014
        COUNT(CASE WHEN m.season = '2013/2014' THEN m.id END) AS matches_2013_2014
    FROM country AS c
    LEFT JOIN match AS m
    ON c.id = m.country_id
    GROUP BY country;

## Conditional selection and summation with CASE WHEN

You can use CASE statements to apply a constraint and run a calculation by including the statement inside an aggregate function like SUM()!

In this exercise, your goal is to filter for a specific team (Real Sociedad) and calculate their total home and away goals per season.

Create a CASE statement to calculate the total number of home goals where the hometeam_id is 8560.
Create a second CASE statement to calculate the total number of away goals where the awayteam_id is 8560, aliasing the column as away_goals.
Group the query by season.

    SELECT season,
        -- SUM the home goals
        SUM(CASE WHEN hometeam_id = 8560 THEN home_goal END) AS home_goals,
        -- SUM the away goals
        SUM(CASE WHEN awayteam_id = 8560 THEN away_goal END) AS away_goals
    FROM match
    -- Group the results by season
    GROUP BY season;

## Using CASE and AVG to derive fractions

CASE statements will return any value you specify in your THEN clause. This is an incredibly powerful tool for robust calculations and data manipulation when used in conjunction with an aggregate statement. One key task you can perform is using CASE inside an AVG function to calculate a fraction of information in your database.

Here's an example of how you set that up:

    AVG(CASE WHEN condition_is_met THEN 1
            WHEN condition_is_not_met THEN 0 END)

With this approach, it's important to accurately specify which records count as 0, otherwise your calculations may not be correct!

Your task is to examine the number of wins, losses, and ties in each country. The matches table is filtered to include all matches from the 2013/2014 and 2014/2015 seasons.

Within an AVG() function, complete the CASE statement by checking if m.home_goal is not equal to m.away_goal, assigning a value of 0 if this condition is met.
Repeat this process to compare home and away goals in '2014/2015', aliasing as ties_2014_2015.

    SELECT 
        c.name AS country,
        -- Calculate the fraction of tied games in each season
        AVG(CASE WHEN m.season='2013/2014' AND m.home_goal = m.away_goal THEN 1
                WHEN m.season='2013/2014' AND m.home_goal != m.away_goal THEN 0
                END) AS ties_2013_2014,
        AVG(CASE WHEN m.season='2014/2015' AND m.home_goal = m.away_goal THEN 1
                WHEN m.season='2014/2015' AND m.home_goal != m.away_goal THEN 0
                END) AS ties_2014_2015
    FROM country AS c
    LEFT JOIN matches AS m
    ON c.id = m.country_id
    GROUP BY country;

