# SQL_Fundamentals
A guide and summary of my practice with SQL Fundamentals 

I best learn by practice and repetition thus i though, why not document it.
Below you will find a number of exercises regarding SQL fundamentals, basic query building, syntax, operators, clauses etc.
All the practice tasks were done using SAKILA example database in MySQL workbench

_________

### Basic `SELECT` Queries
The `SELECT` statement pulls data from database tables. It starts with column names (or * for all) followed by `FROM` to name the table.
```sql
SELECT first_name FROM actor;
```
___
### Column Aliases with `AS`
Aliases rename output columns for clarity using `AS`. Quotes around the alias handle spaces or special characters.
```sql
SELECT name as "category" FROM category;
```
This grabs the first_name column from the actor table and labels the output "Name". It makes results easier to read without changing stored data
___
### Removing Duplicates with `DISTINCT`
`DISTINCT` shows only unique values, skipping repeats.
```sql
SELECT DISTINCT name AS "category" FROM category;
```
___
### Sorting with `ORDER BY`
`ORDER BY` sorts query results. Use `ASC` (ascending, A-Z, low-high) or `DESC` (descending, Z-A, high-low).

```sql
SELECT  first_name AS "name" FROM actor
    ORDER BY first_name ASC;
```
Sorts actor first names alphabetically.

```sql
SELECT title, rental_rate FROM film
   ORDER BY rental_rate DESC;
```
Shows films from most to least expensive.
___
### Aggregation with `COUNT` and `MAX`
`COUNT` tallies rows or unique values. `MAX` finds highest values.

```sql
SELECT COUNT(actor_id) FROM actor;
```
Counts total actors in the table.

```sql
SELECT COUNT(DISTINCT first_name) AS "unique_names" FROM actor;
```
Counts unique first names.

```sql
SELECT MAX(length) FROM film;
```
Returns longest film duration.
___
### Grouping with `GROUP BY`
`GROUP BY` bundles rows by column values, often with aggregates like `COUNT`.

```sql
SELECT COUNT(first_name), first_name FROM actor 
  GROUP BY first_name;
```
Shows count of each first name among actors.
___
### String Functions
`CONCAT` joins text. `LOWER` converts to lowercase.

```sql
SELECT concat(first_name, " ", last_name) FROM actor;
```
Combines first/last names into full names.

```sql
SELECT CONCAT(LOWER(first_name), " ", LOWER(last_name)) AS "actor_full_name" FROM actor;
```
Full names in lowercase, labeled "actor_full_name".
___
### Multiple Columns and `*`
`SELECT` several columns or all `*` with sorting.

```sql
SELECT first_name, last_name FROM actor;
```
Returns both name columns.

```sql
SELECT * FROM actor
  ORDER BY first_name DESC;
```
All actor columns, sorted by first name Z-A.

```sql
SELECT title AS "film_title", description AS "movie_description" FROM film;
```
Film titles/descriptions with clearer labels.
___
### Filtering with `WHERE`
`WHERE` sets conditions to show matching rows only. Use `=`, `>`, `<`, `>=`, `<=`, `BETWEEN`, `IN`, `LIKE`, `IS NULL`.

```sql
SELECT title FROM film
  WHERE length < 90;
```
Films shorter than 90 minutes.

```sql
SELECT title FROM film
  WHERE rental_rate = 3.99 OR length = 130;
```
Films matching either condition.

```sql
SELECT * FROM category
  WHERE name NOT BETWEEN "A%" AND "E%";
```
Categories not starting A-E.

```sql
SELECT title FROM film
  WHERE special_features LIKE "%Commentaries%";
```
Films with "Commentaries" in features.

```sql
SELECT * FROM film WHERE rating = "G";
```
G-rated films.

```sql
SELECT address FROM address WHERE district = "Ahal";
```
Addresses in Ahal district.
___
### Pattern Matching with `LIKE`
`LIKE` finds text patterns: `%` any characters, `_` single character.

```sql
SELECT * FROM category
  WHERE name LIKE "F__I%";
```
Categories like "F?i?" (F, any, any, I, anything).

```sql
SELECT title FROM film
  WHERE title LIKE "ALI%";
```
Titles starting with "ALI".

```sql
SELECT title FROM film
  WHERE title LIKE "_L%";
```
Titles with L as second letter.
___
### Limiting Results with `LIMIT`
`LIMIT` caps returned rows.

```sql
SELECT first_name, last_name FROM customer
  LIMIT 5;
```
First 5 customers.

```sql
SELECT title, length FROM film
  ORDER BY length ASC LIMIT 10;
```
10 shortest films.
___
### `HAVING` for Aggregates
`HAVING` filters grouped results (unlike `WHERE` before grouping).

```sql
SELECT customer_id, date(payment_date), MAX(amount) 
  FROM payment 
  GROUP BY customer_id, date(payment_date)
  HAVING max(amount) > 6.99 
  PRDER BY max(amount) desc;
```
Daily max payments over 6.99.

```sql
SELECT last_name, COUNT(*) FROM actor 
  GROUP BY last_name HAVING COUNT(*) = 1;
```
Last names appearing once.

```sql
SELECT customer_id, SUM(amount) FROM payment 
  GROUP BY customer_id HAVING SUM(amount) BETWEEN 50 AND 100;
```
Customers spending 50-100 total.
___
### `IN` and `NOT IN`
`IN` matches list of values.

```sql
SELECT title, rental_duration, rental_rate FROM film
  WHERE rental_rate >=0.99 AND rental_duration IN(6,7);
```
Rental durations 6 or 7, rate >=0.99.

```sql
SELECT * FROM film
  WHERE rating IN("NC-17","PG");
```
NC-17 or PG rated films.
___
### `IS NULL` and Empty Checks
Checks missing or empty values.

```sql
SELECT address FROM address
  WHERE postal_code IS null OR postal_code = "";
```
Addresses without postal code.
___
### Date Filtering
Use `BETWEEN` or functions for dates.

```sql
SELECT * FROM rental
  WHERE return_date BETWEEN '2005-06-01' AND '2005-06-30';
```
June 2005 returns.
___
### Subqueries
Nested `SELECT` for dynamic values.

```sql
SELECT title, length FROM film 
  WHERE length = (SELECT MAX(length) FROM film);
```
Longest films.

```sql
SELECT * FROM film
  WHERE length > (SELECT AVG(length) FROM film);
```
Films longer than average.
___
### Joins
`JOIN` combines tables on matching keys.

```sql
SELECT a.address, a.postal_code, c.city, co.country
  FROM address a
  join city c ON a.city_id = c.city_id
  JOIN country co ON c.country_id = co.country_id;
```
Addresses with city/country.

```sql
SELECT st.staff_id, SUM(p.amount), COUNT(p.payment_id) FROM payment p
  JOIN staff st ON p.staff_id = st.staff_id
  GROUP BY st.staff_id;
```
Staff payment totals/counts.
___
### `CASE` Statements
Conditional logic like if-then.

```sql
SELECT COUNT(*),
  CASE WHEN active = 1 THEN "active" ELSE "passive" END AS "status" FROM customer
  GROUP BY Statusas;
```
Active/inactive customer counts.

```sql
SELECT CONCAT(first_name, " ", last_name) AS "full_name", SUM(amount) AS "total",
  CASE WHEN SUM(amount) >=100 then "Virš 100" else "Iki 100" end as "Režiai"
  from customer c left join payment p on c.customer_id = p.customer_id
  group by c.customer_id;
```
Customer totals with spending tiers.
___
### String Functions
Manipulate text: `CONCAT`, `LOWER`, `SUBSTR`, `LENGTH`, `LOCATE`.

```sql
SELECT SUBSTR(email, locate("@", email) + 1) AS "domain", COUNT(*) 
  FROM customer GROUP BY domain;
```
Customer email domains.

```sql
SELECT LENGTH(title) FROM film
  WHERE title = "Alone Trip";
```
Character count of title.
___
### Aggregate Math
`AVG`, `SUM`, `ROUND`, `MIN`/`MAX` with groups.

```sql
SELECT SUM(length) / COUNT(*) AS "average_duration" FROM film;
```
Average film length.

```sql
SELECT ROUND(AVG(amount), 2), ROUND(SUM(amount), 2) FROM payment
  GROUP BY staff_id;
```
Rounded staff averages/totals.
