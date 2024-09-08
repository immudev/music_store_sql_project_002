# Music Store Database SQL Project 002

## Project Overview

**Project Title**: Music Store Database  
**Level**: Beginner  
**Database**: `music_store`

## Project Structure

### 1. Database Setup

```sql
CREATE DATABASE music_store;
```


Select query

```sql
select * from album
select * from artist
select * from customer
select * from employee
select * from genre
select * from invoice
select * from invoice_line
select * from media_type
select * from playlist
select * from playlist_track
select * from track
```


1. who is the most employee based on job title.

```sql
SELECT employee_id as empid, first_name, last_name, title
FROM employee
ORDER BY levels DESC
LIMIT 1;
```


2. which country have most invoices.

```sql
SELECT billing_country as country, COUNT(*)
FROM invoice
GROUP BY billing_country
ORDER BY billing_country DESC
LIMIT 1;
```


3. who are the top three values of total inoices.

```sql
SELECT total
FROM invoice
ORDER BY total DESC
LIMIT 3
```


4. which city has the best customers? we would like to throw a promotional music festival in the city we mdae the most money. write a query the returns one city that have the higest sum of inoice totals. return both city name & sum of all inoice totls.

```sql
SELECT billing_city as city, SUM(total) as total_invoice
FROM invoice 
GROUP BY billing_city
ORDER BY SUM(total) DESC
```


5. who is the best customer? the customer who has spend the most money will be declared thr best customer. write a wuery that return the person who spend the most money.

```sql
SELECT c.first_name, c.last_name, SUM(i.total) as spend_most_money
FROM customer c 
JOIN  invoice i ON  c.customer_id = i.customer_id
GROUP BY c.customer_id
ORDER BY spend_most_money DESC
limit 1
```


6. write to return the email, first name, last name and genre of all rock music listners. return your list ordered alph by email starting with a.

```sql
SELECT DISTINCT email, first_name, last_name 
FROM customer
JOIN invoice ON customer.customer_id = invoice.customer_id
JOIN invoice_line ON invoice.invoice_id = invoice.invoice_id
WHERE track_id IN(SELECT track_id FROM track
									JOIN genre ON track.genre_id = genre.genre_id
									WHERE genre.name LIKE 'Rock')
ORDER BY email
```


7. lets invite the artist who have writen the most rock music in our dataset. write a queary the return the artist name and total track count of the top 10 rock bands.

```sql
SELECT ats.artist_id, ats.name, COUNT(ats.artist_id) AS number_of_songs
FROM  track t
JOIN album alb ON alb.album_id =  t.album_id
JOIN artist ats ON ats.artist_id =  alb.artist_id
JOIN genre gnr ON gnr.genre_id = t.genre_id
WHERE gnr.name LIKE 'Rock'
GROUP BY  ats.artist_id
ORDER BY number_of_songs DESC
LIMIT 10
```


8. return all the track names that have a song length longer than the average length return the name and milliseconds for each track order by the songlength with longest songs listed first.

```sql
SELECT name, milliseconds
FROM track
WHERE milliseconds > ( SELECT AVG(milliseconds) AS avg_track_length FROM track)
ORDER BY milliseconds DESC
```


9. find how much amount spent by each customer on artist? write a query to return customer name, artist name and total spent.

```sql
WITH best_selling_artist AS (
SELECT artist.artist_id as artist_id, artist.name as artist_name, SUM(invoice_line.unit_price * invoice_line.quantity) as amount
FROM invoice_line
JOIN track ON track.track_id = invoice_line.track_id
JOIN album ON album.album_id = track.album_id
JOIN artist ON artist.artist_id = album.artist_id
GROUP BY 1
ORDER BY 3 DESC
	)
SELECT c.customer_id, c.first_name, c.last_name, bsa.artist_name, SUM(il.unit_price * il.quantity) AS amount_spent
FROM invoice i
JOIN customer c ON c.customer_id = i.customer_id
JOIN invoice_line il ON il.invoice_id = i.invoice_id
JOIN track t ON t.track_id = il.track_id
JOIN album alb ON alb.album_id = t.album_id
JOIN best_selling_artist bsa ON bsa.artist_id = alb.artist_id
GROUP BY 1,2,3,4
ORDER BY 5 DESC
```


10. we want to find out the most popular music genre for each country. we determine most popular genre as the genre with the higest amount of purchise.write a query that return each country along with the top genre. for countries where the maximum number of purchases is shared return all genres.

```sql
WITH populer_genre as (
SELECT COUNT(invoice_line.quantity) AS pruchases, customer.country, genre.name, genre.genre_id,ROW_NUMBER() OVER(PARTITION BY customer.country ORDER BY COUNT(invoice_line.quantity)DESC) AS RowNo
FROM invoice_line
JOIN invoice ON invoice.invoice_id = invoice_line.invoice_id
JOIN customer ON customer.customer_id = invoice.customer_id
JOIN track ON track.track_id = invoice_line.track_id
JOIN genre ON genre.genre_id = track.genre_id
GROUP BY 2,3,4
ORDER BY 2 ASC, 1 DESC
)
SELECT * FROM populer_genre WHERE RowNo <= 1
```


11. write a query that determine the customer that has spend the most on music for each country. write a query that return the country along with the top customer and how much they spend. for countries where the top amount spend is shared, provide all customer who spend this amount.

```sql
WITH customer_with_country AS (
SELECT customer.customer_id, first_name, last_name, billing_country, SUM(total) AS total_spending,
ROW_NUMBER() OVER(PARTITION BY billing_country ORDER BY SUM(total) DESC) AS RowNO
FROM invoice
JOIN customer ON customer.customer_id = invoice.customer_id
GROUP BY 1,2,3,4
ORDER BY 4 ASC, 5 DESC)
SELECT * FROM customer_with_country WHERE RowNO <= 1
```


## Author - techTFQ

Thank you for your support, and I look forward to connecting with you!
