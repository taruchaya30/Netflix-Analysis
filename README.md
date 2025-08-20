# Netflix-Analysis
This project contains SQL queries to explore and analyze the Netflix dataset, focusing on content trends, categories, casts, directors, and country-specific insights. The analysis is performed in PostgreSQL.


#Project Objectives
The main objectives of this project are:  
1. To **practice SQL queries** on a real-world dataset.  
2. To explore **content trends** on Netflix (type, year, rating, country, genres).  
3. To apply **advanced SQL concepts** like:  
   - Window functions (RANK, COUNT)  
   - CTEs (Common Table Expressions)  
   - String manipulation (`UNNEST`, `STRING_TO_ARRAY`, `SPLIT_PART`)  
   - Date handling (`TO_DATE`, `EXTRACT`, `INTERVAL`)  
4. To gain insights that can help in **business decisions** like:  
   - Which type of content is most popular?  
   - Which actors and directors dominate the platform?  
   - How content trends change over years and countries.  
5. To demonstrate **SQL skills** for portfolio and GitHub projects.


#Schema


```sql
CREATE TABLE NETFLIX(
SHOW_ID VARCHAR(20),
TYPE VARCHAR(10),
TITLE VARCHAR(150),
DIRECTOR VARCHAR(250),
CASTS VARCHAR(1500),
COUNTRY VARCHAR(150),
DATE_ADDED VARCHAR(150),
RELEASE_YEAR INT,
RATING VARCHAR(50),
DURATION VARCHAR(50),
LISTED_IN VARCHAR(100),
DESCRIPTION VARCHAR(250)
)
```


Business Problems and Solutions


1. Count the Number of Movies vs TV Shows

   ```sql
   SELECT TYPE, COUNT(*) AS TOTAL_COUNT 
   FROM NETFLIX
   GROUP BY TYPE
   ```

2. Find the Most Common Rating for Movies and TV Shows

   ```sql
   SELECT TYPE , RATING FROM(
   SELECT TYPE, RATING , COUNT(*) , RANK() OVER(PARTITION BY TYPE ORDER BY COUNT(*) DESC ) AS RANK FROM NETFLIX
   GROUP BY TYPE,RATING) 
   AS TABLE_1
   WHERE RANK = 1
   ```
3. List All Movies Released in a Specific Year (e.g., 2020)

   ```sql
   SELECT * FROM NETFLIX
   WHERE TYPE = 'Movie' AND RELEASE_YEAR = 2020
   ```


4. Find the Top 5 Countries with the Most Content on Netflix

```sql
SELECT DISTINCT(UNNEST(STRING_TO_ARRAY(COUNTRY,','))),COUNT(SHOW_ID) AS CONTENT FROM NETFLIX
WHERE country IS NOT NULL
GROUP BY COUNTRY 
ORDER BY CONTENT DESC LIMIT 5

```

5. Identify the Longest Movie

```sql
SELECT * FROM NETFLIX 
WHERE TYPE = 'Movie'
AND 
DURATION = (SELECT MAX(DURATION) FROM NETFLIX)

```

6.Find Content Added in the Last 5 Years

```sql
SELECT * FROM NETFLIX
WHERE TO_DATE(DATE_ADDED ,'MONTH DD,YYYY') >=CURRENT_DATE - INTERVAL '5 YEARS'
```

7. Find All Movies/TV Shows by Director 'Rajiv Chilaka'

```sql
SELECT * FROM NETFLIX
WHERE DIRECTOR LIKE '%Rajiv Chilaka%'
```

8. List All TV Shows with More Than 5 Seasons

   
```sql
SELECT * FROM  NETFLIX
WHERE TYPE = 'TV Show'
AND 
SPLIT_PART(DURATION , ' ',1)::NUMERIC > 5
```


9. Count the Number of Content Items in Each Genre

```sql
SELECT COUNT(*), UNNEST(STRING_TO_ARRAY(LISTED_IN,',')) AS GENRE FROM NETFLIX
GROUP BY UNNEST(STRING_TO_ARRAY(LISTED_IN,','))

```
10.Find each year and the average numbers of content release in India on netflix

```sql
SELECT EXTRACT(YEAR FROM TO_DATE(DATE_ADDED ,'MONTH DD, YYYY')) as year, COUNT(*) YEAR_WISE_CONTENT,
ROUND(count(*):: numeric /(select count(*) FROM NETFLIX WHERE COUNTRY = 'India')::numeric *100,2) as AVG_CONTENT
from NETFLIX
WHERE COUNTRY = 'India'
group by 1

```


11. List All Movies that are Documentaries

```sql
SELECT * FROM netflix
WHERE listed_in ILIKE '%documentaries%'

```


12. Find All Content Without a Director

```sql
SELECT * FROM netflix
WHERE DIRECTOR IS NULL

```
13. Find How Many Movies Actor 'Salman Khan' Appeared in the Last 10 Years

```sql
SELECT * FROM NETFLIX 
WHERE CASTS ILIKE '%SALMAN KHAN%'
AND 
RELEASE_YEAR > EXTRACT(YEAR FROM CURRENT_DATE - INTERVAL '10 YEARS')
```


14. Find the Top 10 Actors Who Have Appeared in the Highest Number of Movies Produced in India

```sql


SELECT UNNEST(STRING_TO_ARRAY(CASTS,',')) AS HERO , COUNT(*) AS TOTAL_MOVIES FROM NETFLIX
WHERE COUNTRY ILIKE '%INDIA'
GROUP BY 1
ORDER BY 2 DESC
LIMIT 10

```

15. Categorize Content Based on the Presence of 'Kill' and 'Violence' Keywords

```sql
WITH CATEGORY_TABLE AS
(SELECT *, 
CASE
WHEN DESCRIPTION ILIKE '%KILL%'OR DESCRIPTION ILIKE '%VOILENT%' THEN 'BAD_MOVIE' 
ELSE 'GOOD MOVIE'
END AS MOVIE_CATEGORY 
FROM NETFLIX)
SELECT MOVIE_CATEGORY , COUNT(*) AS TOTAL FROM CATEGORY_TABLE
GROUP BY  MOVIE_CATEGORY 

```

#Tech Stack

PostgreSQL
SQL (Window Functions, CTEs, String Functions, Date Functions)




#Author

Taruchaya Shanker
Lucknow, India

   
