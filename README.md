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


Business Problems and Solutions
1. Count the Number of Movies vs TV Shows
   SELECT TYPE, COUNT(*) AS TOTAL_COUNT 
   FROM NETFLIX
   GROUP BY TYPE

2. Find the Most Common Rating for Movies and TV Shows
   SELECT TYPE , RATING FROM(
   SELECT TYPE, RATING , COUNT(*) , RANK() OVER(PARTITION BY TYPE ORDER BY COUNT(*) DESC ) AS RANK FROM NETFLIX
   GROUP BY TYPE,RATING) 
   AS TABLE_1
   WHERE RANK = 1
