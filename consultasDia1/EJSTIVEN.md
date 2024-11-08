## AYELMER

## 20 CONSULTAS

- 5 CONSULTAS **MULTITABLA**
- 5 CONSULTAS **INNER JOIN**
- 5 CONSULTAS **SIMPLES**
- 5 **PROCEDIMIENTOS**



#### BASE DE DATOS USADA :

**SAKILA**





##### Tablas

````SQL
+----------------------------+
| Tables_in_sakila           |
+----------------------------+
| actor                      |
| actor_info                 |
| address                    |
| category                   |
| city                       |
| country                    |
| customer                   |
| customer_list              |
| film                       |
| film_actor                 |
| film_category              |
| film_list                  |
| film_text                  |
| inventory                  |
| language                   |
| nicer_but_slower_film_list |
| payment                    |
| rental                     |
| sales_by_film_category     |
| sales_by_store             |
| staff                      |
| staff_list                 |
| store                      |
+----------------------------+
````



#### CONSULTAS MULTITABLA

1. lista de los clientes que han realizado pagos superiores a $10

   ````sql
   SELECT first_name, last_name
   FROM customer
   WHERE customer_id IN (
       SELECT customer_id
       FROM payment
       WHERE amount > 10
   )
   LIMIT 10;
   
   RESULTADO
   
   +------------+-----------+
   | first_name | last_name |
   +------------+-----------+
   | PATRICIA   | JOHNSON   |
   | LINDA      | WILLIAMS  |
   | NANCY      | THOMAS    |
   | KAREN      | JACKSON   |
   | MICHELLE   | CLARK     |
   | ANGELA     | HERNANDEZ |
   | ANNA       | HILL      |
   | JANET      | PHILLIPS  |
   | JOYCE      | EDWARDS   |
   | DIANE      | COLLINS   |
   +------------+-----------+
   10 rows in set (0.01 sec)
   ````



2.  número total de películas de cada actor

   ````sql
   SELECT first_name, last_name,
          (SELECT COUNT(*)
           FROM film_actor
           WHERE film_actor.actor_id = actor.actor_id) AS total_films
   FROM actor
   LIMIT 10;
   
   RESULTADO
   
   +------------+--------------+-------------+
   | first_name | last_name    | total_films |
   +------------+--------------+-------------+
   | PENELOPE   | GUINESS      |          19 |
   | NICK       | WAHLBERG     |          25 |
   | ED         | CHASE        |          22 |
   | JENNIFER   | DAVIS        |          22 |
   | JOHNNY     | LOLLOBRIGIDA |          29 |
   | BETTE      | NICHOLSON    |          20 |
   | GRACE      | MOSTEL       |          30 |
   | MATTHEW    | JOHANSSON    |          20 |
   | JOE        | SWANK        |          25 |
   | CHRISTIAN  | GABLE        |          22 |
   +------------+--------------+-------------+
   
   ````

   

3. obtener el nombre del cliente que ha realizado el pago más alto

   ````SQL
   SELECT first_name, last_name
   FROM customer
   WHERE customer_id = (
       SELECT customer_id
       FROM payment
       ORDER BY amount DESC
       LIMIT 1
   );
   ````

   

4. queremos encontrar el actor que ha participado en la menor cantidad de películas

   ````SQL
   SELECT first_name, last_name
   FROM actor
   WHERE actor_id = (
       SELECT actor_id
       FROM film_actor
       GROUP BY actor_id
       ORDER BY COUNT(film_id) ASC
       LIMIT 1
   );
   
   RESULTDO 
   
   +------------+-----------+
   | first_name | last_name |
   +------------+-----------+
   | EMILY      | DEE       |
   +------------+-----------+
   ````

5. Queremos encontrar el nombre de un actor que ha participado en al menos una película.

   ````sql
   SELECT first_name, last_name
   FROM actor
   WHERE actor_id IN (
       SELECT actor_id
       FROM film_actor
   )
   LIMIT 10;
   
   RESULTDO
   
   +------------+--------------+
   | first_name | last_name    |
   +------------+--------------+
   | PENELOPE   | GUINESS      |
   | NICK       | WAHLBERG     |
   | ED         | CHASE        |
   | JENNIFER   | DAVIS        |
   | JOHNNY     | LOLLOBRIGIDA |
   | BETTE      | NICHOLSON    |
   | GRACE      | MOSTEL       |
   | MATTHEW    | JOHANSSON    |
   | JOE        | SWANK        |
   | CHRISTIAN  | GABLE        |
   +------------+--------------+
   ````



#### CONSULTAS INNER JOIN

1. Queremos obtener una lista que muestre el título de cada película y su categoría correspondiente.( salen 1000 lo limitare a 10 para mejor lectura)

   ````SQL
   SELECT tbf.title, c.name AS category
   FROM film tbf
   INNER JOIN film_category tbfc ON tbf.film_id = tbfc.film_id
   INNER JOIN category c ON tbfc.category_id = c.category_id
   LIMIT 10;
   
   RESULTDO
   
   +---------------------+----------+
   | title               | category |
   +---------------------+----------+
   | AMADEUS HOLY        | Action   |
   | AMERICAN CIRCUS     | Action   |
   | ANTITRUST TOMATOES  | Action   |
   | ARK RIDGEMONT       | Action   |
   | BAREFOOT MANCHURIAN | Action   |
   | BERETS AGENT        | Action   |
   | BRIDE INTRIGUE      | Action   |
   | BULL SHAWSHANK      | Action   |
   | CADDYSHACK JEDI     | Action   |
   | CAMPUS REMEMBER     | Action   |
   +---------------------+----------+
   ````

   

2. 