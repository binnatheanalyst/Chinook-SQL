**Introduction**
This project explores and analyzes the Chinook Database, a sample dataset that simulates a digital music store. It contains information about artists, albums, media tracks, playlists, customers, invoices, and employees - designed to represent a real-world business scenario similar to platforms like iTunes.

**Skills Demonstrated:** - Advanced SQL queries (JOINs, CTEs, window functions, aggregations).

**Analytical Questions:** - I'll be using SQL to answer the following questions;
What are the most popular genres in each region?
Who are the most productive sales representatives?
What is the average order value and purchase frequency per customer?

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

**Which customers generate the highest revenue by country?**




