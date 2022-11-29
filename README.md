# Práctica 6: Índices y optimización de las BD

## Tareas

### 1. Restauración de la base de datos postgres_air. 

Primero que nada, se debe crear la base de datos, sobre la que restauraremos la información

```sql
CREATE DATABASE hettie;
```

Una vez creada, se restaura la información de la base de datos

```sql
pg_restore -x --no-owner -U postgres -d hettie /home/usuario/postgres_air.backup;
```

### 2. Incluir en la base de datos las siguientes sentencias SQL. ¿Qué acciones realizan?

```sql
SET search_path TO postgres_air;
CREATE INDEX flight_departure_airport ON
flight(departure_airport);
CREATE INDEX flight_scheduled_departure ON postgres_air.flight
(scheduled_departure);
CREATE INDEX flight_update_ts ON postgres_air.flight (update_ts);
CREATE INDEX booking_leg_booking_id ON postgres_air.booking_leg
(booking_id);
CREATE INDEX booking_leg_update_ts ON postgres_air.booking_leg
(update_ts);
CREATE INDEX account_last_name
ON account (last_name);
```

Primeramente estamos indicando el path de la base de datos, para que las siguientes sentencias se ejecuten sobre ella. 
Con las siguientes sentencias, estamos creando índices, para algunos atributos de las tablas de la base de datos. 

Los índices son estructuras de datos que permiten acelerar las búsquedas en las tablas, a costa de un mayor consumo en memoria. 

### 3. Identificar las tablas principales y sus principales elementos.

Las tablas principales son las siguientes:

- **account**: contiene información sobre los clientes de la aerolínea.

- **airport**: contiene información sobre los aeropuertos.

- **flight**: contiene información sobre los vuelos.

- **boarding_pass**: contiene información sobre los billetes de los vuelos.

- **passenger**: contiene información sobre los pasajeros.

- **booking_leg**: contiene información sobre las reservas de los vuelos.


### 4. Obtener un diagrama Entidad-Relación.

Para esta tarea utilizaremos DBeaver, que nos permite generar el diagrama de forma automática.

![Diagrama Entidad-Relación](/img/ER_Diagram.png)

### 5. Realizar la siguiente consulta:

```sql	
SELECT flight_id, scheduled_departure
FROM flight f
JOIN airport a
ON departure_airport=airport_code
AND iso_country='US'
```

#### a) Utilice el comando EXPLAIN para obtener el plan de consulta.

Al usar este comando, se obtiene la siguiente información:

```
QUERY PLAN                                                            |
----------------------------------------------------------------------+
Hash Join  (cost=20.09..17229.60 rows=144636 width=12)                |
  Hash Cond: (f.departure_airport = a.airport_code)                   |
  ->  Seq Scan on flight f  (cost=0.00..15404.76 rows=683176 width=16)|
  ->  Hash  (cost=18.33..18.33 rows=141 width=4)                      |
        ->  Seq Scan on airport a  (cost=0.00..18.33 rows=141 width=4)|
              Filter: (iso_country = 'US'::text)                      |
```

#### b) Lea el resultado del plan y determine el costo total de la consulta, el costo de configuración, la cantidad de filas que se devolverán y cantidad de filas estimadas.

Costo total de la consulta: 17229.60

Costo de configuración: 20.09

Cantidad de filas que se devolverán: XXX

Cantidad de filas estimadas: XXX.

#### c) Repita la consulta del paso (b) de esta actividad, esta vez limitando el número de registros devueltos a 15.

```
QUERY PLAN                                                                                                   |
-------------------------------------------------------------------------------------------------------------+
Limit  (cost=0.70..6.43 rows=15 width=12)                                                                    |
  ->  Merge Join  (cost=0.70..55254.44 rows=144636 width=12)                                                 |
        Merge Cond: (f.departure_airport = a.airport_code)                                                   |
        ->  Index Scan using flight_departure_airport on flight f  (cost=0.42..52031.86 rows=683176 width=16)|
        ->  Index Scan using airport_pkey on airport a  (cost=0.28..67.93 rows=141 width=4)                  |
              Filter: (iso_country = 'US'::text)                                                             |
```

#### d) Revise el plan de consulta actualizado y compare su resultado con el resultado del paso anterior, prestando especial atención a cuántos pasos están involucrados en el plan de consulta y cuál es el costo del paso limitante.

XXX

### 6. Realice la siguiente consulta similar a la del paso anterior.

```sql
SELECT flight_id, scheduled_departure
FROM flight f
JOIN airport a
ON departure_airport=airport_code
AND iso_country='CZ'
```

#### a) Utilice el comando EXPLAIN para obtener el plan de consulta.

```
QUERY PLAN                                                                                     |
-----------------------------------------------------------------------------------------------+
Nested Loop  (cost=20.40..2965.52 rows=1026 width=12)                                          |
  ->  Seq Scan on airport a  (cost=0.00..18.33 rows=1 width=4)                                 |
        Filter: (iso_country = 'CZ'::text)                                                     |
  ->  Bitmap Heap Scan on flight f  (cost=20.40..2936.91 rows=1029 width=16)                   |
        Recheck Cond: (departure_airport = a.airport_code)                                     |
        ->  Bitmap Index Scan on flight_departure_airport  (cost=0.00..20.14 rows=1029 width=0)|
              Index Cond: (departure_airport = a.airport_code)                                 |
```

#### b) Compare los resultados con los obtenidos en la anterior interrogante.

XXX

#### c) Explique la diferencia de rendimiento.

XXX

### 7. Compare estas dos consultas e indique cual es la de mayor rendimiento y la causa. Construya una vista con la que obtengan un mejor rendimiento.

Consulta A:
```sql
SELECT flight_id
,departure_airport
,arrival_airport
FROM flight
WHERE scheduled_arrival BETWEEN
'2020-10-14' AND '2020-10-15';
```

Consulta B:
```sql
SELECT flight_id
,departure_airport
,arrival_airport
FROM flight
WHERE scheduled_arrival:: date='2020-10-14';
```

#### a) Construya índices para con los algoritmos que sean válidos para A y B.

#### b) Evalué el rendimiento (tiempo) que se obtuvieron con lo nuevos índices al realizar las consultas. Construya una tabla con los resultados.

