---
layout: post
author: Rohit vyas
---


## PostgreSQL Table Creation from Command Line Interface (CLI)

## Overview

This guide provides step-by-step instructions for creating a PostgreSQL table using the command line interface (CLI). PostgreSQL is a powerful open-source relational database management system widely used for storing and managing structured data.

## Prerequisites

Before you begin, ensure that you have the following prerequisites:

- PostgreSQL installed on your system
- Access to a PostgreSQL database server
- Basic knowledge of SQL (Structured Query Language)

## Steps

1. **Connect to PostgreSQL Database Server:**
   
   Use the `psql` command-line tool to connect to your PostgreSQL database server:
   ```bash
   psql -U username -d database_name -h host_address -p port_number
    CREATE TABLE table_name (
    column1_name data_type constraints,
    column2_name data_type constraints,
    ...
); 
CREATE TABLE employees (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL
);
\d
```

1. **Insert to PostgreSQL Database Table:**

```
INSERT INTO table_name (column1_name, column2_name, ...)
VALUES (value1, value2, ...);

INSERT INTO employees (name) VALUES ('John Doe');

SELECT * FROM table_name;
```

### Create a Vector Table 
```
CREATE EXTENSION vector;

```
Create a vector column with 3 dimensions
```
CREATE TABLE items (id bigserial PRIMARY KEY, embedding vector(3));
```
 
Insert vectors
```
INSERT INTO items (embedding) VALUES ('[1,2,3]'), ('[4,5,6]');
```

## License

This project is licensed under the [MIT License](LICENSE).


[back](/blogs.html)