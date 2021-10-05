This project required students to generate four SQL queries to answer questions of their own choosing regarding a video rental store dataset provided by Udacity. Of the four queries the student creates, all queries were required to make use of a JOIN and at least 1 aggregation function. 2 of the 4 queries were required to use Common Table Expressions or Subqueries. 1 of the 4 queries was required to use a Window Function. These SQL queries were used to generate a polished visual presentation addressing the student's questions of the database, and these queries were submitted alongside the presentation in a .txt file. 

```
/*Q1: Which staff member made more in total sales?*/
WITH sales_data AS (
        /*Compiles week, total sales, and staff member*/
        SELECT DATE_TRUNC('week', payment.payment_date) AS sales_date,
               staff.first_name || ' ' || staff.last_name AS staff_member,
               SUM(payment.amount) AS total_sales
          FROM payment
               JOIN staff
               ON payment.staff_id = staff.staff_id
        GROUP BY sales_date, staff_member
        ORDER BY sales_date)

/*Window function generates running total by staff member*/
SELECT sales_data.sales_date,
       sales_data.staff_member,
       SUM(sales_data.total_sales) OVER
           (PARTITION BY sales_data.staff_member 
           ORDER BY sales_data.sales_date) AS sales_to_date
  FROM sales_data
ORDER BY staff_member, sales_date;
```

This code block contained a JOIN, an AGGREGATION function, included a Common Table Expression or Subquery, and used a Window Function.

```
/*Q2: What movie genres are most frequently rented?*/
WITH item_rentals AS (
      /*Compiles count of rentals by inventory item, maintains film id*/
      SELECT COUNT(rental.rental_id) AS rentals, 
             inventory.inventory_id, 
             film.film_id
        FROM rental
             JOIN inventory 
             ON rental.inventory_id = inventory.inventory_id
             JOIN film
             ON inventory.film_id = film.film_id
      GROUP BY inventory.inventory_id, film.film_id)

/*Counts joined back into database tables to aggregate by genre*/
SELECT SUM(item_rentals.rentals) AS total_rentals, category.name AS genre
  FROM item_rentals
       JOIN film_category
       ON item_rentals.film_id = film_category.film_id
       JOIN category
       ON film_category.category_id = category.category_id
GROUP BY category.name
ORDER BY total_rentals DESC;
```

This code block contained a JOIN, an AGGREGATION function, and a Common Table Expression or Subquery.

```
/*Q3: Which movies have been rented the most often?*/
WITH rental_counts AS (
      /*Compiles rentals by item, retaining item id and film title*/
      SELECT COUNT(rental.rental_id) AS rental_count, 
             inventory.inventory_id, 
             film.title 
        FROM rental
             FULL JOIN inventory
             ON rental.inventory_id = inventory.inventory_id
             FULL JOIN film
             ON inventory.film_id = film.film_id
      GROUP BY inventory.inventory_id, film.title)

/*Sums up previous counts by film title, also counts copies of title*/
SELECT SUM(rental_counts.rental_count) AS total_rentals, 
       COUNT(rental_counts.inventory_id) AS copies, 
       rental_counts.title
  FROM rental_counts
GROUP BY rental_counts.title
ORDER BY total_rentals DESC;
```

This code block contained a JOIN, an AGGREGATION function, and a Common Table Expression or Subquery.

```
/*Q4: What movie genres have earned the most in total purchases?*/
/*Join across several tables to aggregate total sales by genre*/
SELECT SUM(payment.amount) AS total_sales, 
       category.name AS genre
  FROM payment
       JOIN rental
       ON payment.rental_id = rental.rental_id
       JOIN inventory
       ON rental.inventory_id = inventory.inventory_id
       JOIN film
       ON inventory.film_id = film.film_id
       JOIN film_category
       ON film.film_id = film_category.film_id
       JOIN category
       ON film_category.category_id = category.category_id
GROUP BY category.name
ORDER BY total_sales DESC;
```

This code block contained a JOIN, an AGGREGATION function, and a Common Table Expression or Subquery.