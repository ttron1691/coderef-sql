# coderef-sql

# SQL Reference Card

## Basic SQL Components

### Data Query Language (DQL)

#### SELECT Statement
```sql
SELECT column1, column2, ...
FROM table_name
WHERE condition
GROUP BY column1, column2, ...
HAVING condition
ORDER BY column1 [ASC|DESC]
LIMIT number;
```

#### Common SELECT Clauses
- **WHERE**: Filter records
- **GROUP BY**: Group records with same values
- **HAVING**: Filter groups
- **ORDER BY**: Sort result set
- **LIMIT/OFFSET**: Constrain number of rows returned

#### JOIN Operations
```sql
-- Inner Join
SELECT columns FROM table1
INNER JOIN table2 ON table1.column = table2.column;

-- Left Join
SELECT columns FROM table1
LEFT JOIN table2 ON table1.column = table2.column;

-- Right Join
SELECT columns FROM table1
RIGHT JOIN table2 ON table1.column = table2.column;

-- Full Outer Join
SELECT columns FROM table1
FULL OUTER JOIN table2 ON table1.column = table2.column;

-- Cross Join
SELECT columns FROM table1
CROSS JOIN table2;
```

### Data Manipulation Language (DML)

#### INSERT Statement
```sql
-- Single row
INSERT INTO table_name (column1, column2, ...)
VALUES (value1, value2, ...);

-- Multiple rows
INSERT INTO table_name (column1, column2, ...)
VALUES 
  (value1, value2, ...),
  (value1, value2, ...);

-- Insert from SELECT
INSERT INTO table_name (column1, column2, ...)
SELECT column1, column2, ...
FROM source_table
WHERE condition;
```

#### UPDATE Statement
```sql
UPDATE table_name
SET column1 = value1, column2 = value2, ...
WHERE condition;
```

#### DELETE Statement
```sql
DELETE FROM table_name
WHERE condition;
```

### Data Definition Language (DDL)

#### CREATE Statement
```sql
-- Create database
CREATE DATABASE database_name;

-- Create table
CREATE TABLE table_name (
  column1 datatype constraints,
  column2 datatype constraints,
  ...
  table_constraint1,
  table_constraint2
);

-- Create view
CREATE VIEW view_name AS
SELECT columns
FROM table_name
WHERE condition;

-- Create index
CREATE INDEX index_name
ON table_name (column1, column2, ...);
```

#### ALTER Statement
```sql
-- Add column
ALTER TABLE table_name
ADD column_name datatype;

-- Modify column
ALTER TABLE table_name
MODIFY COLUMN column_name datatype;

-- Drop column
ALTER TABLE table_name
DROP COLUMN column_name;

-- Rename table
ALTER TABLE table_name
RENAME TO new_table_name;
```

#### DROP Statement
```sql
-- Drop table
DROP TABLE table_name;

-- Drop database
DROP DATABASE database_name;

-- Drop view
DROP VIEW view_name;

-- Drop index
DROP INDEX index_name;
```

### Data Control Language (DCL)

```sql
-- Grant privileges
GRANT privilege_name
ON object_name
TO user;

-- Revoke privileges
REVOKE privilege_name
ON object_name
FROM user;
```

### Transaction Control Language (TCL)

```sql
BEGIN TRANSACTION;
-- SQL statements
COMMIT;  -- Or ROLLBACK to undo

-- Savepoints
SAVEPOINT savepoint_name;
-- SQL statements
ROLLBACK TO savepoint_name;
```

## Data Types

### Numeric Types
- **INTEGER/INT**: Whole number
- **SMALLINT**: Small integer
- **BIGINT**: Large integer
- **DECIMAL(p,s)/NUMERIC(p,s)**: Exact decimal (precision, scale)
- **FLOAT/REAL**: Approximate numeric values
- **DOUBLE PRECISION**: Double precision floating point

### String Types
- **CHAR(n)**: Fixed-length character string
- **VARCHAR(n)**: Variable-length character string
- **TEXT**: Variable unlimited length

### Date/Time Types
- **DATE**: Date (YYYY-MM-DD)
- **TIME**: Time (HH:MM:SS)
- **TIMESTAMP**: Date and time
- **INTERVAL**: Period of time

### Other Types
- **BOOLEAN**: TRUE/FALSE/NULL
- **BINARY/VARBINARY**: Binary strings
- **BLOB**: Binary large objects
- **JSON**: JSON data
- **XML**: XML data
- **UUID**: Universal unique identifier

## Advanced SQL Techniques

### Subqueries
```sql
-- Subquery in SELECT
SELECT column1, (SELECT AVG(column2) FROM table2) AS avg_col2
FROM table1;

-- Subquery in WHERE
SELECT column1, column2
FROM table1
WHERE column1 > (SELECT AVG(column1) FROM table1);

-- Subquery in FROM
SELECT t.avg_value
FROM (SELECT AVG(column1) AS avg_value FROM table1) t;

-- Correlated subquery
SELECT column1, column2
FROM table1 t1
WHERE column1 > (SELECT AVG(column1) FROM table1 t2 WHERE t2.column2 = t1.column2);
```

### Common Table Expressions (CTEs)
```sql
WITH cte_name AS (
  SELECT column1, column2
  FROM table_name
  WHERE condition
)
SELECT * FROM cte_name;

-- Multiple CTEs
WITH 
  cte1 AS (SELECT * FROM table1),
  cte2 AS (SELECT * FROM table2)
SELECT * FROM cte1 JOIN cte2 ON cte1.id = cte2.id;

-- Recursive CTE
WITH RECURSIVE cte_name AS (
  -- Base case
  SELECT column1, column2 FROM table_name WHERE condition
  UNION ALL
  -- Recursive case
  SELECT t.column1, t.column2 
  FROM table_name t
  JOIN cte_name c ON t.parent_id = c.id
)
SELECT * FROM cte_name;
```

### Window Functions
```sql
-- Row_number
SELECT column1, 
       ROW_NUMBER() OVER (PARTITION BY column2 ORDER BY column3) AS row_num
FROM table_name;

-- Rank
SELECT column1,
       RANK() OVER (ORDER BY column2) AS rank_val
FROM table_name;

-- Dense_rank
SELECT column1,
       DENSE_RANK() OVER (ORDER BY column2) AS dense_rank_val
FROM table_name;

-- Ntile
SELECT column1,
       NTILE(4) OVER (ORDER BY column2) AS quartile
FROM table_name;

-- Lead/Lag
SELECT column1, column2,
       LAG(column2) OVER (ORDER BY column1) AS prev_value,
       LEAD(column2) OVER (ORDER BY column1) AS next_value
FROM table_name;

-- First_value/Last_value
SELECT column1,
       FIRST_VALUE(column2) OVER (PARTITION BY column3 ORDER BY column1) AS first_val,
       LAST_VALUE(column2) OVER (PARTITION BY column3 ORDER BY column1
                            RANGE BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING) AS last_val
FROM table_name;

-- Sum/Avg/Min/Max
SELECT column1, column2,
       SUM(column2) OVER (PARTITION BY column3) AS sum_val,
       AVG(column2) OVER (PARTITION BY column3) AS avg_val
FROM table_name;
```

### CASE Expressions
```sql
-- Simple CASE
SELECT column1,
       CASE column2
           WHEN value1 THEN result1
           WHEN value2 THEN result2
           ELSE result3
       END AS new_column
FROM table_name;

-- Searched CASE
SELECT column1,
       CASE
           WHEN column2 > value1 THEN result1
           WHEN column2 > value2 THEN result2
           ELSE result3
       END AS new_column
FROM table_name;
```

### Set Operations
```sql
-- UNION (removes duplicates)
SELECT column1 FROM table1
UNION
SELECT column1 FROM table2;

-- UNION ALL (keeps duplicates)
SELECT column1 FROM table1
UNION ALL
SELECT column1 FROM table2;

-- INTERSECT
SELECT column1 FROM table1
INTERSECT
SELECT column1 FROM table2;

-- EXCEPT
SELECT column1 FROM table1
EXCEPT
SELECT column1 FROM table2;
```

### Pivot and Unpivot
```sql
-- Simple Pivot (SQL Server syntax)
SELECT column1,
       [category1], [category2], [category3]
FROM (
    SELECT column1, category, value
    FROM source_table
) AS SourceTable
PIVOT (
    SUM(value)
    FOR category IN ([category1], [category2], [category3])
) AS PivotTable;

-- Unpivot (SQL Server syntax)
SELECT column1, category, value
FROM PivotTable
UNPIVOT (
    value
    FOR category IN ([category1], [category2], [category3])
) AS UnpivotTable;
```

### JSON Handling
```sql
-- PostgreSQL JSON
SELECT column1, json_column->>'property' AS property_value
FROM table_name;

-- MySQL JSON
SELECT column1, JSON_EXTRACT(json_column, '$.property') AS property_value
FROM table_name;
```

### Full-Text Search
```sql
-- MySQL
SELECT * FROM table_name
WHERE MATCH(column1, column2) AGAINST('search term' IN BOOLEAN MODE);

-- PostgreSQL
SELECT * FROM table_name
WHERE to_tsvector('english', column1) @@ to_tsquery('search:*');
```

## SQL Best Practices

### Query Optimization
- Use specific columns in SELECT instead of SELECT *
- Use appropriate indexes for frequently queried columns
- Avoid using functions on indexed columns in WHERE clauses
- Limit result sets with WHERE conditions
- Use EXPLAIN/EXPLAIN ANALYZE to understand query execution plans
- Avoid nested loops when possible
- Prefer JOINs over subqueries when appropriate
- Use LIMIT for pagination

### Database Design
- Normalize data to reduce redundancy (typically 3NF)
- Use appropriate data types (avoid oversized types)
- Implement proper constraints (PRIMARY KEY, FOREIGN KEY, UNIQUE)
- Use meaningful naming conventions
- Create appropriate indexes:
  - Primary keys (automatically indexed)
  - Foreign keys
  - Columns often used in WHERE clauses
  - Columns used in JOIN conditions

### Security
- Use parameterized queries to prevent SQL injection
- Limit user privileges using GRANT/REVOKE
- Encrypt sensitive data
- Validate and sanitize all inputs
- Implement row-level security when needed
- Use views to restrict access to sensitive columns

### Transactions
- Use transactions for operations that must be atomic
- Keep transactions short to reduce locking
- Use appropriate isolation levels for your use case
- Consider using optimistic concurrency control for high-contention systems

### Maintenance
- Regularly backup databases
- Schedule index maintenance (rebuilding/reorganizing)
- Update statistics regularly
- Monitor query performance
- Implement partitioning for very large tables
- Consider archiving old data

## Database-Specific Features

### MySQL/MariaDB
- AUTO_INCREMENT for identity columns
- ENUM and SET types
- Triggers and stored procedures
- Full-text search capabilities
- JSON data type support
- Virtual columns

### PostgreSQL
- Rich data types (arrays, hstore, geometric types)
- Table inheritance
- Advanced indexing (GIN, GiST, BRIN)
- JSON/JSONB support with rich operators
- Materialized views
- Full-text search with advanced language support
- Custom operators and aggregates

### SQL Server
- Identity columns
- Computed columns
- Temporary tables (#temp and ##global_temp)
- Table variables
- MERGE statement
- TRY...CATCH error handling
- Window functions
- Common Table Expressions (CTEs)

### Oracle
- ROWID pseudocolumn
- CONNECT BY for hierarchical queries
- Materialized views
- Advanced partitioning options
- PL/SQL procedural language
- Analytical functions
- SEQUENCE objects

### SQLite
- Lightweight file-based database
- Dynamic typing
- Virtual tables
- Full-text search with FTS3/FTS4/FTS5
- R-Tree spatial index support
