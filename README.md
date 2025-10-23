**Introduction**
This project explores and analyzes the Chinook Database, a sample dataset that simulates a digital music store. It contains information about artists, albums, media tracks, playlists, customers, invoices, and employees - designed to represent a real-world business scenario similar to platforms like iTunes.

**Skills Demonstrated:** - Advanced SQL queries (JOINs, window functions, aggregations, CTE, conditional logic and views)

To start off, The Chinook database contains 11 tables - I have generated the ERD diagram showing the relationship between all 11 tables
Below is an Entity Relationship Diagram that shows the relationship between the tables.

<img width="650" height="728" alt="image" src="https://github.com/user-attachments/assets/8d6fb1bc-8ffa-4097-9951-a44690d019f4" /> 



**1. What are the top 5 best-selling artists by total sales?**
This query identifies the five artists who have generated the highest total sales revenue in the Chinook music store.
It works by: Joining the track, invoiceline, album, and artist tables to link each track purchase back to its artist.
Summing the UnitPrice from invoiceline to calculate total revenue per artist.
Grouping results by artist name to get total sales for each artist.
Ordering the results in descending order and limiting the output to the top five artists.

<img width="633" height="566" alt="image" src="https://github.com/user-attachments/assets/af22ac5b-85e2-4b97-b9a2-a6cfc2e76fda" /> 




**2. Highest revenue generators (Top 5 Countries, Top 5 customers, Top 5 revenue customers per country?**
This analysis identifies the top-performing segments in the Chinook database by revenue.
It highlights the Top 5 Countries, Top 5 Customers, and 
Top 5 Revenue-Generating Customers per Country using SQL aggregation, window functions, ranking, partitioning and subquery.
These insights help visualize where the business earns the most and who its most valuable customers are.


<img width="716" height="818" alt="image" src="https://github.com/user-attachments/assets/95ba834a-63c7-41b5-b0fc-3e6d229b1e35" />


2(c) seems a bit complicated as it contains an inner query that aggregates customer revenue which is basically the query in 2(b). 
Then the RANK window function and PARTITION BY country which simply means
- start ranking over again for each country (each country would have it's own rank list)
- rank them in descending order of revenue then finally - the outer query which keeps only the top 5 performing customers per country based on revenue rank.
NB:- Inner Query is a subquery which only aggregates customer revenue by using the JOIN function.



**3. Deepdive into Genre and it's insights?**
In this section, I explored the relationship between the Genre and Track tables to uncover deeper insights into music trends.
By joining both tables, I analyzed key metrics such as most popular genres by country, purchases per genre, running totals per genre.
This analysis also revealed revenue growth patterns over time, helping to understand how listener preferences evolve.
Overall, this section showcases how combining multiple tables can turn raw music data into meaningful business insights. 

<img width="716" height="818" alt="image" src="https://github.com/user-attachments/assets/ba55e375-e64e-40c9-ae54-a1d766eb256a" />



**4. Classification and Labelling**
Grouping data based on conditions can be achieved using Conditional Logic - this moves beyond basic analysis into modelling and business logic.
This solves questions like 
1. Which customers are our top spenders, and how can we segment them into value tiers?
2. Which genres sell the most tracks, and how can we classify them by popularity?

<img width="820" height="818" alt="image" src="https://github.com/user-attachments/assets/7c0acdcd-4880-4a86-a78c-d85a18933f22" />

Case statements mainly used for Customer Segmentation, Revenue Grouping, Product Grouping - basically categorizing.


**5. Building modular analysis layers (Data Modelling)**
I grew really in love with SQL VIEWS - it works like a saved query that behaves like an actual table.
But it's not an actual table, it is a stored SELECT statement that you can query later.

Why do I find this interesting;
1. Simplifies complex queries for re-use
2. Limit what others can see (Protecting raw data).

<img width="1003" height="577" alt="image" src="https://github.com/user-attachments/assets/c10a062b-fd4b-4e75-877d-59a712d1d1b1" />


If you have any questions or suggestions as regards my solutions to this, you can send me a message on twitter

Thank you for reading! Hopefully you'll read about my next case study too, right? See you soon.









