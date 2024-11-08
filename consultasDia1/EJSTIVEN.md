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
   
   +-------------------+---------+
   |title              | category|
   +-------------------+---------+
   |AMADEUS HOLY       | Action  |
   |AMERICAN CIRCUS    | Action  |
   |ANTITRUST TOMATOES | Action  |
   |ARK RIDGEMONT      | Action  |
   |BAREFOOT MANCHURIAN| Action  |
   |BERETS AGENT       | Action  |
   |BRIDE INTRIGUE     | Action  |
   |BULL SHAWSHANK     | Action  |
   |CADDYSHACK JEDI    | Action  |
   |CAMPUS REMEMBER    | Action  |
   +-------------------+---------+
   ````

2. Obtén una lista de películas junto con el nombre de sus categorías.
la limite a 10 porque salen 1000 de todas las categorias pero para revision mostrar 10 de las 10 primeras

SINTAXIS 
````SQL
SELECT columns
FROM tabla1 AS alias1
INNER JOIN tabla2 AS alias2 ON alias1.campo_comun = alias2.campo_comun
INNER JOIN tabla3 AS alias3 ON alias2.campo_comun = alias3.campo_comun;
````
````sql
   SELECT f.film_id, f.title, c.name AS category_name
   FROM film AS f
   INNER JOIN film_category AS fc ON f.film_id = fc.film_id
   INNER JOIN category AS c ON fc.category_id = c.category_id
   ORDER BY category_name DESC
   LIMIT 10;

   RESULTADO

   +-------+--------------------+-------------+
   |film_id|title               |category_name|
   +-------+--------------------+-------------+
   |     41|ARSENIC INDEPENDENCE|Travel       |
   |     57|BASIC EASY          |Travel       |
   |     75|BIRD INDEPENDENCE   |Travel       |
   |     84|BOILED DARES        |Travel       |
   |     87|BOONDOCK BALLROOM   |Travel       |
   |     88|BORN SPINAL         |Travel       |
   |    103|BUCKET BROTHERHOOD  |Travel       |
   |    123|CASABLANCA SUPER    |Travel       |
   |    125|CASSIDY WYOMING     |Travel       |
   |    167|COMA HEAD           |Travel       |
   +-------+--------------------+-------------+
   10 rows in set (0.00 sec)
````

3. Muestra los clientes y sus respectivas direcciones, uniendo las tablas de clientes y direcciones.

#### SINTAXIS 
````SQL
   SELECT alias_tabla1.columna1, alias_tabla1.columna2, alias_tabla2.columna3
   FROM tabla1 AS alias_tabla1
   INNER JOIN tabla2 AS alias_tabla2 ON alias_tabla1.campo_comun = alias_tabla2.campo_comun;
````
````sql
   SELECT cu.first_name AS nombre, cu.last_name AS apellido, a.address AS direccion
   FROM customer AS cu
   INNER JOIN address AS a ON cu.address_id = a.address_id
   LIMIT 10;
````

   RESULTADO
````SQL
   +---------+--------+----------------------------------+
   | nombre  |apellido|direccion                         |
   +---------+--------+----------------------------------+
   |MARY     |SMITH   |1913 Hanoi Way                    |
   |PATRICIA |JOHNSON |1121 Loja Avenue                  |
   |LINDA    |WILLIAMS|692 Joliet Street                 |
   |BARBARA  |JONES   |1566 Inegl Manor                  |
   |ELIZABETH|BROWN   |53 Idfu Parkway                   |
   |JENNIFER |DAVIS   |1795 Santiago de Compostela Way   |
   |MARIA    |MILLER  |900 Santiago de Compostela Parkway|
   |SUSAN    |WILSON  |478 Joliet Way                    |
   |MARGARET |MOORE   |613 Korolev Drive                 |
   |DOROTHY  |TAYLOR  |1531 Sal Drive                    |
   +---------+------+------------------------------------+
````

4. Muestra los actores y las películas en las que han participado, uniendo las tablas de actores y películas.

#### SINTAXIS 
````SQL
   SELECT columns
   FROM tabla1 AS alias1
   INNER JOIN tabla2 AS alias2 ON alias1.campo_comun = alias2.campo_comun
   INNER JOIN tabla3 AS alias3 ON alias2.campo_comun = alias3.campo_comun;
````
````sql
   SELECT f.film_id, f.title, a.first_name AS actor
   FROM film AS f
   INNER JOIN film_actor AS fa ON f.film_id = fa.film_id
   INNER JOIN actor AS a ON fa.actor_id = a.actor_id
   ORDER BY actor
   LIMIT 10;
````

   RESULTADO
````SQL
   +-------+---------------------+-----+
   |film_id|title                |actor|
   +-------+---------------------+-----+
   |     26|ANNIE IDENTITY       |ADAM |
   |     52|BALLROOM MOCKINGBIRD |ADAM |
   |    233|DISCIPLE MOTHER      |ADAM |
   |    317|FIREBALL PHILADELPHIA|ADAM |
   |    359|GLADIATOR WESTWARD   |ADAM |
   |    362|GLORY TRACY          |ADAM |
   |    385|GROUNDHOG UNCUT      |ADAM |
   |    399|HAPPINESS UNITED     |ADAM |
   |    450|IDOLS SNATCHERS      |ADAM |
   |    532|LOSER HUSTLER        |ADAM |
   +-------+---------------------+-----+
   10 rows in set (0.01 sec)
````

5. Lista los empleados y la dirección de la tienda en la que trabajan, uniendo las tablas de empleados, tiendas y direcciones.


#### SINTAXIS 
````SQL
   SELECT alias_tabla1.columna1, alias_tabla2.columna2
   FROM tabla1 AS alias_tabla1
   INNER JOIN tabla2 AS alias_tabla2 ON alias_tabla1.campo_comun = alias_tabla2.campo_comun
   INNER JOIN tabla3 AS alias_tabla3 ON alias_tabla2.otro_campo_comun = alias_tabla3.otro_campo_comun;


````
````sql
   SELECT s.first_name AS nombre_empleado, s.last_name AS apellido_empleado, a.address AS direccion_tienda
   FROM staff AS s
   INNER JOIN store AS st ON s.store_id = st.store_id
   INNER JOIN address AS a ON st.address_id = a.address_id;


````
RESULTADO
````SQL
   +-------+---------------------+-----+
   |film_id|title                |actor|
   +-------+---------------------+-----+
   |     26|ANNIE IDENTITY       |ADAM |
   |     52|BALLROOM MOCKINGBIRD |ADAM |
   |    233|DISCIPLE MOTHER      |ADAM |
   |    317|FIREBALL PHILADELPHIA|ADAM |
   |    359|GLADIATOR WESTWARD   |ADAM |
   |    362|GLORY TRACY          |ADAM |
   |    385|GROUNDHOG UNCUT      |ADAM |
   |    399|HAPPINESS UNITED     |ADAM |
   |    450|IDOLS SNATCHERS      |ADAM |
   |    532|LOSER HUSTLER        |ADAM |
   +-------+---------------------+-----+
   10 rows in set (0.01 sec)
````

#### CONSULTAS SIMPLES

1. Muestra todos los actores de la base de datos, con su primer y último nombre. la limite a 20 porque salen 200 para mejor revision

#### SINTAXIS
````SQL
   SELECT columna1 AS alias1, columna2 AS alias2
   FROM nombre_tabla;
````

````SQL
   SELECT first_name AS nombre, last_name AS apellido
   FROM actor
   LIMIT 20;
````

RESULTADO

````SQL
+---------+------------+
|nombre   |apellido    |
+---------+------------+
|PENELOPE |GUINESS     |
|NICK     |WAHLBERG    |
|ED       |CHASE       |
|JENNIFER |DAVIS       |
|JOHNNY   |LOLLOBRIGIDA|
|BETTE    |NICHOLSON   |
|GRACE    |MOSTEL      |
|MATTHEW  |JOHANSSON   |
|JOE      |SWANK       |
|CHRISTIAN|GABLE       |
|ZERO     |CAGE        |
|KARL     |BERRY       |
|UMA      |WOOD        |
|VIVIEN   |BERGEN      |
|CUBA     |OLIVIER     |
|FRED     |COSTNER     |
|HELEN    |VOIGHT      |
|DAN      |TORN        |
|BOB      |FAWCETT     |
|LUCILLE  |TRACY       |
+---------+------------+
````

2. Muestra el título de todas las películas junto con su descripción.

#### SINTAXIS
````SQL
   SELECT columna1 , columna2
   FROM nombre_tabla
   LIMIT cantidad;
````

````SQL
   SELECT title, description 
   FROM film
   LIMIT 10;
````

RESULTADO

````SQL
+----------------+---------------------------------------------------------------------------------------------------------------------+
|title           |description                                                                                                          |
+----------------+---------------------------------------------------------------------------------------------------------------------+
|ACADEMY DINOSAUR|A Epic Drama of a Feminist And a Mad Scientist who must Battle a Teacher in The Canadian Rockies                     |
|ACE GOLDFINGER  |A Astounding Epistle of a Database Administrator And a Explorer who must Find a Car in Ancient China                 |
|ADAPTATION HOLES|A Astounding Reflection of a Lumberjack And a Car who must Sink a Lumberjack in A Baloon Factory                     |
|AFFAIR PREJUDICE|A Fanciful Documentary of a Frisbee And a Lumberjack who must Chase a Monkey in A Shark Tank                         |
|AFRICAN EGG     |A Fast-Paced Documentary of a Pastry Chef And a Dentist who must Pursue a Forensic Psychologist in The Gulf of Mexico|
|AGENT TRUMAN    |A Intrepid Panorama of a Robot And a Boy who must Escape a Sumo Wrestler in Ancient China                            |
|AIRPLANE SIERRA |A Touching Saga of a Hunter And a Butler who must Discover a Butler in A Jet Boat                                    |
|AIRPORT POLLOCK |A Epic Tale of a Moose And a Girl who must Confront a Monkey in Ancient India                                        |
|ALABAMA DEVIL   |A Thoughtful Panorama of a Database Administrator And a Mad Scientist who must Outgun a Mad Scientist in A Jet Boat  |
|ALADDIN CALENDAR|A Action-Packed Tale of a Man And a Lumberjack who must Reach a Feminist in Ancient China                            |
+----------------+---------------------------------------------------------------------------------------------------------------------+
````
3. Lista los clientes que esten en estado activo.
la database arroja 584, lo limite a 10

#### SINTAXIS
````SQL
   SELECT columna1 , columna2
   FROM nombre_tabla
   WHERE columna3 = 'contenido'
   LIMIT cantidad;
````

````SQL
   SELECT first_name, active
   FROM customer
   WHERE active = '1'
   LIMIT 10;
````

RESULTADO

````SQL
+----------+------+
|first_name|active|
+----------+------+
|MARY      |     1|
|PATRICIA  |     1|
|LINDA     |     1|
|BARBARA   |     1|
|ELIZABETH |     1|
|JENNIFER  |     1|
|MARIA     |     1|
|SUSAN     |     1|
|MARGARET  |     1|
|DOROTHY   |     1|
+----------+------+
````
4. listar el nombre y el correo electrónico de los clientes, ordenando los resultados primero por correo electrónico de manera descendente.

#### SINTAXIS
````SQL
   SELECT columna1 , columna2
   FROM nombre_tabla
   ORDER BY columna ASC
   LIMIT cantidad;
````

````SQL
   SELECT email, first_name 
   FROM customer
   ORDER BY email DESC
   LIMIT 10;
````

RESULTADO

````SQL
+--------------------------------------+----------+
|email                                 |first_name|
+--------------------------------------+----------+
|ZACHARY.HITE@sakilacustomer.org       |ZACHARY   |
|YVONNE.WATKINS@sakilacustomer.org     |YVONNE    |
|YOLANDA.WEAVER@sakilacustomer.org     |YOLANDA   |
|WILMA.RICHARDS@sakilacustomer.org     |WILMA     |
|WILLIE.MARKHAM@sakilacustomer.org     |WILLIE    |
|WILLIE.HOWELL@sakilacustomer.org      |WILLIE    |
|WILLIAM.SATTERFIELD@sakilacustomer.org|WILLIAM   |
|WILLARD.LUMPKIN@sakilacustomer.org    |WILLARD   |
|WESLEY.BULL@sakilacustomer.org        |WESLEY    |
|WENDY.HARRISON@sakilacustomer.org     |WENDY     |
+--------------------------------------+----------+
````
5. Muestra el total de rentas realizadas por cada cliente, junto con su nombre y apellido.

#### SINTAXIS
````SQL
   SELECT columna1, columna2, AGREGADO(columna3) AS alias
   FROM tabla1
   JOIN tabla2 ON tabla1.columna_común = tabla2.columna_común
   GROUP BY columna_grupo;
````

````SQL
   SELECT c.first_name, c.last_name, COUNT(r.rental_id) AS total_rentas
   FROM customer c
   JOIN rental r ON c.customer_id = r.customer_id
   GROUP BY c.customer_id
   LIMIT 10;
````

RESULTADO

````SQL
+----------+---------+------------+
|first_name|last_name|total_rentas|
+----------+---------+------------+
|MARY      |SMITH    |          32|
|PATRICIA  |JOHNSON  |          27|
|LINDA     |WILLIAMS |          26|
|BARBARA   |JONES    |          22|
|ELIZABETH |BROWN    |          38|
|JENNIFER  |DAVIS    |          28|
|MARIA     |MILLER   |          33|
|SUSAN     |WILSON   |          24|
|MARGARET  |MOORE    |          23|
|DOROTHY   |TAYLOR   |          25|
+----------+---------+------------+
````


### PROCEDIMIENTOS

DELIMITER $$
CREATE PROCEDURE TotalRentasPorCliente(IN p_customer_id INT)
BEGIN
SELECT COUNT(r.rental_id) AS total_rentas
FROM rental r
WHERE r.customer_id = p_customer_id;
END $$
Query OK, 0 rows affected (0.01 sec)