In this chapter, you'll learn how to use aggregate functions you're familiar with, like `AVG()` and `SUM()`, as window functions, as well as how to define frames to change a window function's output.

## Running totals of athlete medals

The running total (or cumulative sum) of a column helps you determine what each row's contribution is to the total sum.

Return the athletes, the number of medals they earned, and the medals running total, ordered by the athletes' names in alphabetical order.

    WITH Athlete_Medals AS (
    SELECT
        Athlete, COUNT(*) AS Medals
    FROM Summer_Medals
    WHERE
        Country = 'USA' AND Medal = 'Gold'
        AND Year >= 2000
    GROUP BY Athlete)

    SELECT
    -- Calculate the running total of athlete medals
    athlete,
    Medals,
    SUM(Medals) OVER (ORDER BY athlete ASC) AS Max_Medals
    FROM Athlete_Medals
    ORDER BY Athlete ASC;

## Maximum country medals by year

Getting the maximum of a country's earned medals so far helps you determine whether a country has broken its medals record by comparing the current year's earned medals and the maximum so far.

Return the year, country, medals, and the maximum medals earned so far for each country, ordered by year in ascending order.

    WITH Country_Medals AS (
    SELECT
        Year, Country, COUNT(*) AS Medals
    FROM Summer_Medals
    WHERE
        Country IN ('CHN', 'KOR', 'JPN')
        AND Medal = 'Gold' AND Year >= 2000
    GROUP BY Year, Country)

    SELECT
    -- Return the max medals earned so far per country
    year,
    country,
    Medals,
    MAX(Medals) OVER (PARTITION BY country
                    ORDER BY year ASC) AS Max_Medals
    FROM Country_Medals
    ORDER BY Country ASC, Year ASC;

## Minimum country medals by year

So far, you've seen MAX and SUM, aggregate functions normally used with GROUP BY, being used as window functions. You can also use the other aggregate functions, like MIN, as window functions.

Return the year, medals earned, and minimum medals earned so far.

    WITH France_Medals AS (
    SELECT
        Year, COUNT(*) AS Medals
    FROM Summer_Medals
    WHERE
        Country = 'FRA'
        AND Medal = 'Gold' AND Year >= 2000
    GROUP BY Year)

    SELECT
    year,
    Medals,
    MIN(Medals) OVER (ORDER BY year ASC) AS Min_Medals
    FROM France_Medals
    ORDER BY Year ASC;

# Frames

## Moving maximum of Scandinavian athletes' medals

Frames allow you to restrict the rows passed as input to your window function to a sliding window for you to define the start and finish.

Adding a frame to your window function allows you to calculate "moving" metrics, inputs of which slide from row to row.

Return the year, medals earned, and the maximum medals earned, comparing only the current year and the next year.

    WITH Scandinavian_Medals AS (
    SELECT
        Year, COUNT(*) AS Medals
    FROM Summer_Medals
    WHERE
        Country IN ('DEN', 'NOR', 'FIN', 'SWE', 'ISL')
        AND Medal = 'Gold'
    GROUP BY Year)

    SELECT
    -- Select each year's medals
    Year,
    Medals,
    -- Get the max of the current and next years'  medals
    MAX(Medals) OVER (ORDER BY Year ASC
                ROWS BETWEEN CURRENT ROW
                AND 1 FOLLOWING) AS Max_Medals
    FROM Scandinavian_Medals
    ORDER BY Year ASC;

## Moving maximum of Chinese athletes' medals

Frames allow you to "peek" forwards or backward without first using the relative fetching functions, LAG and LEAD, to fetch previous rows' values into the current row.

Return the athletes, medals earned, and the maximum medals earned, comparing only the last two and current athletes, ordering by athletes' names in alphabetical order.

    WITH Chinese_Medals AS (
    SELECT
        Athlete, COUNT(*) AS Medals
    FROM Summer_Medals
    WHERE
        Country = 'CHN' AND Medal = 'Gold'
        AND Year >= 2000
    GROUP BY Athlete)

    SELECT
    -- Select the athletes and the medals they've earned
    Athlete,
    Medals,
    -- Get the max of the last two and current rows' medals 
    MAX(Medals) OVER (ORDER BY Athlete ASC
                ROWS BETWEEN 2 PRECEDING
                AND CURRENT ROW) AS Max_Medals
    FROM Chinese_Medals
    ORDER BY Athlete ASC;

# Moving average and totals

## Moving average of Russian medals

Using frames with aggregate window functions allow you to calculate many common metrics, including moving averages and totals. These metrics track the change in performance over time.

Calculate the 3-year moving average of medals earned.

    WITH Russian_Medals AS (
    SELECT
        Year, COUNT(*) AS Medals
    FROM Summer_Medals
    WHERE
        Country = 'RUS'
        AND Medal = 'Gold'
        AND Year >= 1980
    GROUP BY Year)

    SELECT
    Year, Medals,
    --- Calculate the 3-year moving average of medals earned
    AVG(Medals) OVER
        (ORDER BY Year ASC
        ROWS BETWEEN
        2 PRECEDING AND CURRENT ROW) AS Medals_MA
    FROM Russian_Medals
    ORDER BY Year ASC;

## Moving total of countries' medals

What if your data is split into multiple groups spread over one or more columns in the table? Even with a defined frame, if you can't somehow separate the groups' data, one group's values will affect the average of another group's values.

Calculate the 3-year moving sum of medals earned per country.

    `WITH Country_Medals AS (
    SELECT
        Year, Country, COUNT(*) AS Medals
    FROM Summer_Medals
    GROUP BY Year, Country)

    SELECT
    Year, Country, Medals,
    -- Calculate each country's 3-game moving total
    SUM(Medals) OVER
        (PARTITION BY country
        ORDER BY Year ASC
        ROWS BETWEEN
        2 PRECEDING AND CURRENT ROW) AS Medals_MA
    FROM Country_Medals
    ORDER BY Country ASC, Year ASC;`

