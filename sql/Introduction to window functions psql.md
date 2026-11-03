## Numbering Olympic games in ascending order

The Summer Olympics dataset contains the results of the games between 1896 and 2012. The first Summer Olympics were held in 1896, the second in 1900, and so on. What if you want to easily query the table to see in which year the 13th Summer Olympics were held? You'd need to number the rows for that.

Assign a number to each year in which Summer Olympic games were held.

    SELECT
    Year,

    -- Assign numbers to each year
    ROW_NUMBER() OVER() AS Row_N
    FROM (
    SELECT DISTINCT year
    FROM Summer_Medals
    ORDER BY Year ASC
    ) AS Years
    ORDER BY Year ASC;

# Order By

Numbering Olympic games in descending order
You've already numbered the rows in the Summer Medals dataset. What if you need to reverse the row numbers so that the most recent Olympic games' rows have a lower number?

Assign a number to each year in which Summer Olympic games were held so that rows with the most recent years have lower row numbers.

    SELECT
    Year,
    -- Assign the lowest numbers to the most recent years
    ROW_NUMBER() OVER (ORDER BY Year DESC) AS Row_N
    FROM (
    SELECT DISTINCT Year
    FROM Summer_Medals
    ) AS Years
    ORDER BY Year;

## umbering Olympic athletes by medals earned

Row numbering can also be used for ranking. For example, numbering rows and ordering by the count of medals each athlete earned in the OVER clause will assign 1 to the highest-earning medalist, 2 to the second highest-earning medalist, and so on.

For each athlete, count the number of medals he or she has earned.

    SELECT
    -- Count the number of medals each athlete has earned
    athlete,
    COUNT(medal) AS Medals
    FROM Summer_Medals
    GROUP BY Athlete
    ORDER BY Medals DESC;

Having wrapped the previous query in the Athlete_Medals CTE, rank each athlete by the number of medals they've earned.

    WITH Athlete_Medals AS (
    SELECT
        -- Count the number of medals each athlete has earned
        Athlete,
        COUNT(*) AS Medals
    FROM Summer_Medals
    GROUP BY Athlete)

    SELECT
    -- Number each athlete by how many medals they've earned
    athlete,
    ROW_NUMBER() OVER (ORDER BY Medals DESC) AS Row_N
    FROM Athlete_Medals
    ORDER BY Medals DESC;

## Reigning weightlifting champions

A reigning champion is a champion who's won both the previous and current years' competitions. To determine if a champion is reigning, the previous and current years' results need to be in the same row, in two different columns.

Return each year's gold medalists in the Men's 69KG weightlifting competition.

    SELECT
    -- Return each year's champions' countries
    year,
    country AS champion
    FROM Summer_Medals
    WHERE
    Discipline = 'Weightlifting' AND
    Event = '69KG' AND
    Gender = 'Men' AND
    Medal = 'Gold';

Having wrapped the previous query in the Weightlifting_Gold CTE, get the previous year's champion for each year.


    WITH Weightlifting_Gold AS (
    SELECT
        -- Return each year's champions' countries
        Year,
        Country AS champion
    FROM Summer_Medals
    WHERE
        Discipline = 'Weightlifting' AND
        Event = '69KG' AND
        Gender = 'Men' AND
        Medal = 'Gold')

    SELECT
    Year, Champion,
    -- Fetch the previous year's champion
    LAG(Champion) OVER
        (ORDER BY Year ASC) AS Last_Champion
    FROM Weightlifting_Gold
    ORDER BY Year ASC;

## Reigning champions by gender

You've already fetched the previous year's champion for one event. However, if you have multiple events, genders, or other metrics as columns, you'll need to split your table into partitions to avoid having a champion from one event or gender appear as the previous champion of another event or gender.

Return the previous champions of each year's event by gender.

    WITH Tennis_Gold AS (
    SELECT DISTINCT
        Gender, Year, Country
    FROM Summer_Medals
    WHERE
        Year >= 2000 AND
        Event = 'Javelin Throw' AND
        Medal = 'Gold')

    SELECT
    Gender, Year,
    Country AS Champion,
    -- Fetch the previous year's champion by gender
    LAG(Country) OVER (PARTITION BY Gender
                ORDER BY Year ASC) AS Last_Champion
    FROM Tennis_Gold

## Reigning champions by gender and event

In the previous exercise, you partitioned by gender to ensure that data about one gender doesn't get mixed into data about the other gender. If you have multiple columns, however, partitioning by only one of them will still mix the results of the other columns.

Return the previous champions of each year's events by gender and event.

    WITH Athletics_Gold AS (
    SELECT DISTINCT
        Gender, Year, Event, Country
    FROM Summer_Medals
    WHERE
        Year >= 2000 AND
        Discipline = 'Athletics' AND
        Event IN ('100M', '10000M') AND
        Medal = 'Gold')

    SELECT
    Gender, Year, Event,
    Country AS Champion,
    -- Fetch the previous year's champion by gender and event
    LAG(Country) OVER (PARTITION BY Gender, Event
                ORDER BY Year ASC) AS Last_Champion
    FROM Athletics_Gold
    ORDER BY Event ASC, Gender ASC, Year ASC;

