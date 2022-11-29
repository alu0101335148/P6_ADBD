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

Con las sentencias anteriores estamos creando índices para algunos atributos de las tablas de la base de datos. Los índices son estructuras de datos que permiten acelerar las búsquedas en las tablas. 

### 3. Identificar las tablas principales y sus principales elementos.


### 4. 