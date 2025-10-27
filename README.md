# 🎵 Chinook SQL Analysis

##  Overview
This project explores the **Chinook** sample database using SQL to uncover business insights related to music sales, customer behavior, and revenue trends.  
The analysis was performed using **MySQL** leveraging SQL joins, aggregate functions, window functions, and CTEs.

---

## Database Schema
The Chinook database simulates a digital music store, including tables for:
- **Artists**
- **Albums**
- **Tracks**
- **Genres**
- **Invoices**
- **Customers**
- **Employees**

---

## Key Questions Answered
1. Top 5 best-selling artists by total sales  
2. Highest-revenue customers by country  
3. Average order value and purchase frequency per customer  
4. Most popular music genres by region  
5. Running total and revenue growth per genre over time

---
To start off, The Chinook database contains 11 tables - I have generated the ERD diagram showing the relationship between all 11 tables
Below is an Entity Relationship Diagram that shows the relationship between the tables.

<img width="650" height="728" alt="image" src="https://github.com/user-attachments/assets/8d6fb1bc-8ffa-4097-9951-a44690d019f4" /> 


---

## Queries & Insights
### 1. Top 5 Best-Selling Artists
This query identifies the five artists who have generated the highest total sales revenue in the Chinook music store.
It works by: Joining the track, invoiceline, album, and artist tables to link each track purchase back to its artist.
Summing the UnitPrice from invoiceline to calculate total revenue per artist.
Grouping results by artist name to get total sales for each artist.
Ordering the results in descending order and limiting the output to the top five artists.

```sql
SELECT art.name AS ArtistName, 
       SUM(il.UnitPrice * il.Quantity) AS TotalSales
FROM invoiceLine AS il
JOIN track AS t ON il.TrackId = t.TrackId
JOIN album AS a ON t.AlbumId = a.AlbumId
JOIN artist AS art ON a.ArtistId = art.ArtistId
GROUP BY art.ArtistId, art.Name
ORDER BY TotalSales DESC
LIMIT 5;

```
---

### 2. Highest revenue generators.
This analysis identifies the top-performing segments in the Chinook database by revenue.
It highlights the 
- Top 5 Countries
- Top 5 Customers and
- Top 5 Revenue-Generating Customers per Country

Using SQL aggregation, window functions, ranking, partitioning and subquery.
These insights help visualize where the business earns the most and who its most valuable customers are.

```sql

-- 2a. Top 5 countries that generated the highest revenue ?
SELECT CST.country, SUM(INV.Total) AS Revenue
FROM customer AS CST 
JOIN invoice AS INV ON INV.customerid = CST.customerid
GROUP BY CST.country
ORDER BY Revenue DESC
LIMIT 5;

```

```sql
-- 2b. Top 5 customers that generated the highest revenue ?
SELECT CST.country, INV.customerid, CST.firstname, CST.lastname, SUM(INV.total) as Revenue 
FROM Invoice as INV
JOIN customer AS CST ON CST.customerid = INV.customerid
GROUP BY CST.country, INV.customerid, CST.firstname, CST.lastname
ORDER BY Revenue DESC
LIMIT 5;

```

```sql
-- 2c. Top 5 highest revenue customers per country using a window function ? 
SELECT country, firstname, lastname, Revenue, Revenue_Rank
FROM (
SELECT CST.country, CST.firstname, CST.lastname, SUM(INV.total) as Revenue,
RANK() OVER	(PARTITION BY CST.country
ORDER BY SUM(INV.total) DESC ) as Revenue_Rank
FROM customer as CST
JOIN invoice as INV ON CST.customerid = INV.customerid
GROUP BY CST.country, INV.customerid, CST.firstname, CST.lastname)  as ranked_customers
WHERE revenue_rank <= 5
ORDER BY country, revenue_rank DESC;

```

2(c) seems a bit complicated as it contains an inner query that aggregates customer revenue which is basically the query in 2(b). 
Then the RANK window function and PARTITION BY country which simply means
- start ranking over again for each country (each country would have it's own rank list)
- rank them in descending order of revenue then finally
- the outer query which keeps only the top 5 performing customers per country based on revenue rank.

**NB:- Inner Query is a subquery which only aggregates customer revenue by using the JOIN function**

---

### **3. Deepdive into Genre and it's insights?**

In this section, I explored the relationship between the Genre and Track tables to uncover deeper insights into music trends.
By joining both tables, I analyzed key metrics such as;
- most popular genres by country
- purchases per genre
- running totals per genre.
- revenue growth over time
This analysis helps to understand how listener preferences evolve.

Overall, this section showcases how combining multiple tables can turn raw music data into meaningful business insights.

```sql

-- 3a. Most popular Genre By Country ?	
SELECT CST.country as country, GEN.name AS GenreName, count(*) as Purchases
FROM invoiceline AS IL 
JOIN invoice as INV on INV.invoiceid = IL.invoiceid
JOIN customer AS CST on CST.customerid = INV.customerid
JOIN track AS T ON T.trackid = IL.trackid
JOIN genre AS GEN ON GEN.genreid = T.genreid
GROUP BY country, genrename
ORDER BY Purchases DESC
LIMIT 5;

```

```sql

-- 3b. Purchases Per Genre ?
SELECT GEN.Genreid, GEN.name AS GenreName, IL.unitprice, IL.quantity, 
count(*) AS purchases FROM invoiceline AS IL
JOIN track as T ON T.trackid = IL.trackid
JOIN genre as GEN ON gen.genreid = T.genreid
GROUP BY GEN.Genreid, GenreName, IL.unitprice, IL.quantity
ORDER BY Purchases DESC;

```

```sql

-- 3c. Running totals Per Genre ?
SELECT GEN.genreid, Gen.name AS GenreName, IL.unitprice, IL.quantity,
(IL.UnitPrice * IL.Quantity) AS LineTotal,
SUM(IL.UnitPrice * IL.Quantity) OVER (PARTITION BY GEN.Genreid
ORDER BY IL.invoicelineid ) as RunningTotal 
FROM invoiceline as IL
JOIN track as T ON T.trackid = IL.trackid
JOIN genre as GEN ON gen.genreid = T.genreid
ORDER BY GenreName, IL.invoicelineid;

```

```sql

-- 3d. Revenue Growth Over Time ?
SELECT GEN.Name AS GenreName, i.InvoiceDate, SUM(IL.UnitPrice * IL.Quantity) AS DailyRevenue,
SUM(SUM(IL.UnitPrice * IL.Quantity)) OVER (PARTITION BY GEN.Name
ORDER BY i.InvoiceDate) AS RunningTotal
FROM InvoiceLine AS IL
JOIN Track AS T ON IL.TrackId = T.TrackId
JOIN Genre AS GEN ON GEN.GenreId = T.GenreId
JOIN Invoice AS i ON IL.InvoiceId = i.InvoiceId
GROUP BY GEN.Name, i.InvoiceDate
ORDER BY GEN.Name, i.InvoiceDate;

```

---

### 4. Classification and Labelling

Grouping data based on conditions can be achieved using Conditional Logic - this moves beyond basic analysis into modelling and business logic.
This solves questions like;
- Which customers are our top spenders, and how can we segment them into value tiers?
- Which genres sell the most tracks, and how can we classify them by popularity?

```sql
-- 4a. Classify customers by spending level
SELECT 
    c.FirstName,
    c.LastName,
    SUM(i.Total) AS TotalSpent,
    CASE
        WHEN SUM(i.Total) > 40 THEN 'High-Value'
        WHEN SUM(i.Total) BETWEEN 20 AND 40 THEN 'Medium-Value'
        ELSE 'Low-Value'
    END AS CustomerTier
FROM Customer AS c
JOIN Invoice AS i ON c.CustomerId = i.CustomerId
GROUP BY c.CustomerId, c.FirstName, c.LastName;

```

```sql

-- 4b. Categorize genres by popularity
SELECT 
    g.Name AS Genre,
    COUNT(il.InvoiceLineId) AS Purchases,
    CASE
        WHEN COUNT(il.InvoiceLineId) > 100 THEN 'Top Genre'
        WHEN COUNT(il.InvoiceLineId) BETWEEN 50 AND 100 THEN 'Moderate'
        ELSE 'Low Popularity'
    END AS Popularity
FROM Genre g
JOIN Track t ON g.GenreId = t.GenreId
JOIN InvoiceLine il ON t.TrackId = il.TrackId
GROUP BY g.Name
ORDER BY Purchases DESC;

```

Case statements are mainly used for Customer Segmentation, Revenue Grouping, Product Grouping - basically categorizing.

---

c 5. Building modular analysis layers (Data Modelling)

I grew really in love with SQL VIEWS - it works like a saved query that behaves like an actual table.
But it's not an actual table, it is a stored SELECT statement that you can query later.

Why do I find this interesting ?
- Simplifies complex queries for re-use
- Limit what others can see (Protecting raw data)

```sql

/* Create a view for customer revenue
Question Solved: Who are our top customers by country ?
*/
CREATE VIEW customer_revenue AS
SELECT c.CustomerId, c.FirstName, c.LastName, c.Country, SUM(i.Total) AS TotalSpent
FROM Customer AS c
JOIN Invoice AS i ON c.CustomerId = i.CustomerId
GROUP BY c.CustomerId, c.FirstName, c.LastName, c.Country;

```

```sql

/* Create a view for genre sales summary
Question Solved: Which music genres generate the most revenue?
*/
CREATE VIEW genre_sales AS
SELECT g.Name AS Genre, SUM(il.UnitPrice * il.Quantity) AS TotalRevenue, 
COUNT(il.InvoiceLineId) AS TotalPurchases
FROM Genre g
JOIN Track t ON g.GenreId = t.GenreId
JOIN InvoiceLine il ON t.TrackId = il.TrackId
GROUP BY g.Name;

```

---

### Tools & Technologies

- Database: MySQL (Chinook dataset)
- Environment: MYSQL Workbench
- Language: SQL

### Project Learnings

- Writing advanced SQL queries with joins, aggregation, and window functions

- Using CTEs to organize complex logic

- Building reusable analytical views

- Presenting data insights through SQL-based exploration

### Author

Obinna - (binnatheanalyst)
Data Analyst | SQL • Python • Power BI • Tableau •

📫 [Connect with me on LinkedIn](https://www.linkedin.com/in/joseph-obinna-2a3b811b3/)

If you have any questions or suggestions as regards my solutions to this, you can send me a message on twitter

Thank you for reading! Hopefully you'll read about my next case study too, right? 

See you soon.









