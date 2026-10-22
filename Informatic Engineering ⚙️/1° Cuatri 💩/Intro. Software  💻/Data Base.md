-  **Organize** information storage
- Make easy the searching, recovering and handling of data
- They are important to web applications
## Parts
- **Data Base**: such information
- **Engine**: the in charge of keeping and work with such information
- **Gestor/Client**: the interfaz
## Engine more used
- PostgreSQL
- MySQL
- SQL Server
## Relational DB
- Organice data in ==tables==: field and columns
- Every table save one kind of thing: athletes, sports, venues
- Every field is identified by an ID
- The tables can link each other

Example:

| name  | last name | country | sport |
| ----- | --------- | ------- | ----- |
| ..... | ......    | .....   | ..... |

## Structured Query Language (SQL)
It's a standar language to handle relational data bases. SQL is everywhere
### What we can do?
- Create and modify data bases
- Search information
- Define tables structure
### SQL vs PostgreSQL

| SQL                        | PostgreSQL                                      |
| -------------------------- | ----------------------------------------------- |
| It's the language          | It's the engine                                 |
| It can run in every engine | Add some syntax that can't run in other engines |

## Basic commands
Every sentence end with `;`
![[Pasted image 20260924201822.png]]
### Create and delete data bases
To create
```SQL
CREATE DATABASE <db_name>;
```
To delete
```SQL
DROP DATABASE <db_name>;
```

### Create a table
```SQL
CREATE TABLE clientes (
    id INT,
    nombre VARCHAR(50),
    apellido VARCHAR(50)
);
```

### Data Types
- Whole numbers: `INT` (or `INTENGER`), `SMALLINT`, `BIGINT`
- Decimal numbers: `DECIMAL(10, 2)`
- Text: 
	- `VARCHAR(50)` ---> variable length
	- `CHAR(3)` ---> fixed length
	- `TEXT` ---> without limit
- Booleans: `BOOLEAN`
- Date and time: `Date`(`2026-9-12`), `TIME`, `TIMESTAMP`

### Insert data
```SQL
INSERT INTO clientes (id, nombre, apellido)
VALUES (1, 'Juan', 'Pérez');
```
- In parenthesis we indicate which column we will fill and in what order

### Delete fields
```SQL
DELETE FROM clientes WHERE id = 1;
```
- Delete all fields that match with the condition `WHERE`

### Modify row
```SQL
UPDATE clientes
SET apellido = 'García'
WHERE id = 2; ---> Optional
```
- If we forget the `WHERE` every row that match will be updated
### Query
```SQL
SELECT nombre, apellido FROM clientes WHERE ciudad = 'Madrid';
```
- `SELECT` ---> which column wanna see
- `FROM` ---> which table wanna see
- `WHERE` ---> filter (optional)
- `*` ---> wild card "all columns"

## Primary Key (PK)
It's a attribute that we choose to identify uniquely every row
- It must to be uniq
- Stable
- One per table, it can identify a lot of uniq data, but only one is the identifier

## Adding constraints
``` SQL
CREATE TABLE deportistas (
    id INT PRIMARY KEY,
    nombre VARCHAR(50) NOT NULL,
    apellido VARCHAR(50) NOT NULL,
    pais VARCHAR(50) NOT NULL,
    deporte VARCHAR(50) NOT NULL,
    prueba VARCHAR(100)
);
```

## `Unique` vs `Primary Key`

| `Unique`                 | `Primary Key`                |
| ------------------------ | ---------------------------- |
| It's unique              | It's `unique` and `not null` |
| Acept `NULL`             | Once per table               |
| You can have a lot of it |                              |

## Foreign key
A `Foreign key` is a database validation rule applied to a column in a **child table** that forces its values to match and  existing `PRIMARY KEY` in a **parent table**

``` SQL
-- Create Parent Table
CREATE TABLE users (
    user_id SERIAL PRIMARY KEY, --> Auto-incrementing primary key
    username VARCHAR(50) NOT NULL
);

-- 2. Create Child Table using inline REFERENCES
CREATE TABLE ssh_keys (
    key_id SERIAL PRIMARY KEY,
    user_id INT REFERENCES users(user_id), --> Foreign key linking to users.user_id
    key_label VARCHAR(50) NOT NULL
);
```

### Execution Rules
```SQL
-- Valid Insertion: user_id = 1 exists in users table
INSERT INTO ssh_keys (user_id, key_label) 
VALUES (1, 'laptop_ed25519'); --> SUCCESS

-- Invalid Insertion: user_id = 999 DOES NOT exist in users table
INSERT INTO ssh_keys (user_id, key_label) 
VALUES (999, 'rogue_key'); --> ERROR: violates foreign key constraint
```