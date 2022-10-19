This project uses SQL to answer the question: 'When was the Golden Age of Video Games'. It combines joins, set theory and other important SQL skills that work 
through user_ratings, critic reviews and sales to answer this question. 


When Was The Golden Age of Video Games

Project Description:
This project analyzes video game critic and user scores as well as sales data for the top 400 video games released since 1977. I will attempt to quantify the ‘Golden Age’ of video games by identifying release years that users and critics liked best as well as exploring the business side by looking at game sales data as well.
Skills Used: Joining & Comparing Datasets with Set theory, Grouping, Filtering & Ordering Data

Task 1: Find the ten best selling games in game_sales
SELECT *
FROM game_sales
ORDER BY games_sold DESC
LIMIT 10;
By using DESC the results will be shown in descending order this will put the best selling game at the top.
So far with the data returned the best years are between 1985 and 2017

Task 2: Determine how many games in the game_sales table are missing both a user score and a critic score
SELECT COUNT(g.game)
FROM game_sales AS g
LEFT JOIN reviews AS R
ON g.game = r.game
WHERE r.critic_score IS NULL AND r.user_score IS NULL;
31 rows have come back as NULL, this comes to 7.75% of the data set has NULL values.


Task 3: Now to start measuring for which year was the ‘Golden Age’, search the critic scores for the highest average year.
SELECT g.year, 
	     ROUND(AVG(r.critic_score), 2) AS avg_critic_score
FROM game_sales AS g
LEFT JOIN reviews AS r
ON g.game = r.game
GROUP BY g.year
ORDER BY avg_critic_score DESC
LIMIT 10;
The data returns a range between 1982 and 2020 as the best years.
One more point of note here is that 1982 returns a round number of 9.0. This could be a fault in the data set, as it’s an usual sight for averages. 


Task 4: Update the previous query so that there is a better filter. Include a COUNT where games were released in a given year, that year being any year that more than 4 games were released.
SELECT 
    g.year,
    ROUND(AVG(r.critic_score), 2) AS avg_critic_score,
    COUNT(g.game) AS num_games
FROM game_sales g
INNER JOIN reviews r
ON g.game = r.game
GROUP BY g.year
HAVING COUNT(g.game) > 4
ORDER BY avg_critic_score DESC 
LIMIT 10;

The returned data paints a much better picture and shows years that had a good amount of reviewed games. Some of the years have dropped off the table.


Task 5: Use set theory to find the years that were on the first critic favorite’s list, but not the second.
SELECT 
    year, 
    avg_critic_score
FROM top_critic_years
EXCEPT
SELECT year, avg_critic_score
FROM top_critic_years_more_than_four_games
ORDER BY avg_critic_score DESC;

From this it looks like the early 90’s would be a strong contender for the ‘Golden Age’ of video games. 

Task 6: Update the query from task 4, so that it returns the years with the 10 highest average user scores.
SELECT
    g.year,
    COUNT(g.game) AS num_games,
    ROUND(AVG(r.user_score), 2) AS avg_user_score  
FROM game_sales g
INNER JOIN reviews r
ON g.game = r.game
GROUP BY g.year
HAVING COUNT(g.game) > 4
ORDER BY avg_user_score DESC
LIMIT 10;

Now we have a top ten list that includes both critic reviews ad user reviews.




Task 7: Create a list of years that appear in both the ‘top_critic_years_more_than_four_games’ table and the ‘top_user_years_more_than_four_games’ table
SELECT year
FROM top_critic_years_more_than_four_games
INTERSECT
SELECT year
FROM top_user_years_more_than_four_games;

Now we have a list of 3 years that are contenders for the ‘Golden Age’. There is another point to consider though, the sales.

Task 8: Add a column to the previous query showing total games sold.
SELECT 
    g.year, 
    SUM(g.games_sold) AS total_games_sold
FROM game_sales g
WHERE g.year IN (SELECT year
FROM top_user_years_more_than_four_games
INTERSECT
SELECT year
FROM top_critic_years_more_than_four_games)
GROUP BY g.year
ORDER BY total_games_sold DESC;



