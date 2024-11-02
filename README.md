
# Business Intelligence and Sales Analytics for a Digital Music Store

## Project Overview
This project consists of SQL queries designed to extract key business insights from a music store database. The database contains tables such as albums, artists, customers, invoices, and tracks. These queries range from basic operations to advanced data analysis, answering questions about customer spending, sales trends, and music preferences.

## Goals
*   Analyze customer behavior by identifying top customers and sales trends.
*   Gain business insights on music genre popularity and artist sales performance.
*   Show proficiency in using SQL for data retrieval through joins, subqueries, window functions, and CTEs.
*   Solve practical business problems for a music store, including identifying best-selling genres and customer purchasing patterns.

## Contents
### 1. Connecting to the Database
To start, we connect to the music_store_DB and view the available tables:

    \c music_store_DB;  
    
    -- View tables  
    SELECT * FROM album;  
    SELECT * FROM artist;  
    SELECT * FROM customer;  
    SELECT * FROM invoice;

2. Basic Questions
These queries address simple business inquiries such as top-performing employees, countries with the most invoices, and top customers.

#### Q1: Senior-most employee based on job title
    SELECT title, last_name, first_name  
    FROM employee  
    ORDER BY levels DESC  
    LIMIT 1;

This query sorts employees by their level of seniority and selects the topmost employee.

#### Q2: Which countries have the most invoices?
    SELECT COUNT(*) AS invoice_count, billing_country   
    FROM invoice  
    GROUP BY billing_country  
    ORDER BY invoice_count DESC;

This query counts invoices per country and orders the results to show the countries with the most sales activity.

#### Q5: Who is the best customer?
    SELECT customer.customer_id, first_name, last_name, SUM(total) AS total_spending  
    FROM customer  
    JOIN invoice ON customer.customer_id = invoice.  customer_id  
    GROUP BY customer.customer_id, customer.first_name,   customer.last_name  
    ORDER BY total_spending DESC  
    LIMIT 1;  

This query identifies the customer who has spent the most by summing the total invoice amounts.

## 3. Moderate Question Set
This section delves into more complex queries involving multiple table joins and filtering.

#### Q1: List of Rock music listeners
    SELECT DISTINCT email, first_name, last_name  
    FROM customer  
    JOIN invoice ON customer.customer_id = invoice.  customer_id  
    JOIN invoice_line ON invoice.invoice_id = invoice_line.invoice_id  
    JOIN track ON track.track_id = invoice_line.track_id  
    JOIN genre ON genre.genre_id = track.genre_id  
    WHERE genre.name = 'Rock'  
    ORDER BY email;  

This query identifies all customers who have purchased Rock music tracks.

#### Q2: Top 10 rock bands with the most songs
    SELECT artist.artist_id, artist.name, COUNT(track.track_id) AS number_of_songs  
    FROM track  
    JOIN album ON album.album_id = track.album_id  
    JOIN artist ON artist.artist_id = album.artist_id 
    JOIN genre ON genre.genre_id = track.genre_id  
    WHERE genre.name = 'Rock'  
    GROUP BY artist.artist_id, artist.name  
    ORDER BY number_of_songs DESC  
    LIMIT 10;

### 4. Advanced Question Set
These queries involve more sophisticated data analysis using CTEs and window functions to generate deep business insights.  

#### Q1: Amount spent by each customer on the best-selling artist
    WITH best_selling_artist AS (  
        SELECT artist.artist_id, artist.name AS   artist_name, SUM(invoice_line.unit_price *   invoice_line.quantity) AS total_sales  
        FROM invoice_line  
        JOIN track ON track.track_id = invoice_line.  track_id  
        JOIN album ON album.album_id = track.album_id  
        JOIN artist ON artist.artist_id = album.artist_id  
        GROUP BY artist.artist_id, artist.name  
        ORDER BY total_sales DESC  
        LIMIT 1  
    )  
    SELECT c.customer_id, c.first_name, c.last_name, bsa.artist_name, SUM(il.unit_price * il.quantity) AS amount_spent  
    FROM invoice i  
    JOIN customer c ON c.customer_id = i.customer_id  
    JOIN invoice_line il ON il.invoice_id = i.invoice_id  
    JOIN track t ON t.track_id = il.track_id  
    JOIN album alb ON alb.album_id = t.album_id  
    JOIN best_selling_artist bsa ON bsa.artist_id = alb.artist_id  
    GROUP BY c.customer_id, c.first_name, c.last_name, bsa.artist_name  
    ORDER BY amount_spent DESC;  

This query calculates how much each customer has spent on tracks by the best-selling artist.

#### Q2: Most popular music genre for each country
    WITH popular_genre AS (  
        SELECT COUNT(invoice_line.quantity) AS purchases, customer.country, genre.name,   
               ROW_NUMBER() OVER (PARTITION BY customer.  country ORDER BY COUNT(invoice_line.quantity) DESC) AS row_no  
        FROM invoice_line  
        JOIN invoice ON invoice.invoice_id = invoice_line.invoice_id  
        JOIN customer ON customer.customer_id = invoice.customer_id  
        JOIN track ON track.track_id = invoice_line.track_id  
        JOIN genre ON genre.genre_id = track.genre_id
        GROUP BY customer.country, genre.name  
    )  
    SELECT *   
    FROM popular_genre   
    WHERE row_no = 1;

This query identifies the most popular genre in each country based on track purchases.


## 5. Complex SQL Features
The queries utilize a variety of advanced SQL techniques:

*   Joins: Used to connect multiple tables (e.g., customer, invoice, track) to access related data.
*   CTEs (Common Table Expressions): Used to create modular and reusable query components.
*   Window Functions: For ranking data within partitions (e.g., ranking genres within each country).
*   Aggregation: For summarizing data, such as counting invoices or summing total purchases.

### How to Run  

*   Connect to the Database: Ensure you have access to the music_store_DB.
*   Run Queries: Copy and paste the SQL queries into a database client (e.g., PostgreSQL, MySQL) and execute them to retrieve results.
*   Analyze Results: Use the query output to answer business questions about customer spending, music preferences, and artist performance.

### Key Insights

*   Customer Spending: Identified the best customer in terms of total spending and who has purchased from the best-selling artist.
*   Popular Genres: Determined the most popular genre in different countries.
*   Top Artists and Tracks: Found the top rock bands and tracks with longer-than-average durations.

### Conclusion

This project demonstrates how SQL can be applied to analyze customer behavior, sales trends, and music preferences in a music store database. The queries provide actionable insights that can be used for marketing, inventory management, and decision-making.
