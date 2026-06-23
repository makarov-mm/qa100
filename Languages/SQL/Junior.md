# SQL Junior Interview Questions

Question-answer interview preparation file for SQL at Junior level.

## Question 001

### Question
What is SQL?

### Answer
SQL is a language used to query, define, and manipulate data in relational databases. It is used for reading data, inserting rows, updating records, deleting records, defining tables, and controlling access.

## Question 002

### Question
What is a relational database?

### Answer
A relational database stores data in tables with rows and columns. Tables can be related through keys, usually primary keys and foreign keys.

## Question 003

### Question
What is a table?

### Answer
A table is a structured collection of rows with a defined set of columns. Each row represents one record.

## Question 004

### Question
What is a row?

### Answer
A row is a single record in a table.

## Question 005

### Question
What is a column?

### Answer
A column defines one attribute of a table and has a data type.

## Question 006

### Question
What is a primary key?

### Answer
A primary key uniquely identifies each row in a table. It must be unique and should not be null.

## Question 007

### Question
What is a foreign key?

### Answer
A foreign key is a column or group of columns that references a primary key or unique key in another table, enforcing referential integrity.

## Question 008

### Question
What is SELECT?

### Answer
`SELECT` retrieves data from one or more tables.

## Question 009

### Question
What is WHERE?

### Answer
`WHERE` filters rows according to a condition.

## Question 010

### Question
What is ORDER BY?

### Answer
`ORDER BY` sorts query results by one or more columns.

## Question 011

### Question
What is GROUP BY?

### Answer
`GROUP BY` groups rows so aggregate functions can be applied to each group.

## Question 012

### Question
What is HAVING?

### Answer
`HAVING` filters grouped results. It is similar to `WHERE`, but it is applied after grouping.

## Question 013

### Question
What is the difference between WHERE and HAVING?

### Answer
`WHERE` filters rows before grouping. `HAVING` filters groups after `GROUP BY`.

## Question 014

### Question
What is INSERT?

### Answer
`INSERT` adds new rows to a table.

## Question 015

### Question
What is UPDATE?

### Answer
`UPDATE` modifies existing rows in a table.

## Question 016

### Question
What is DELETE?

### Answer
`DELETE` removes rows from a table.

## Question 017

### Question
What is NULL?

### Answer
`NULL` means missing or unknown value. It is not the same as zero, false, or an empty string.

## Question 018

### Question
How do you compare values with NULL?

### Answer
Use `IS NULL` or `IS NOT NULL`. Expressions like `= NULL` do not work as expected because NULL represents unknown.

## Question 019

### Question
What is INNER JOIN?

### Answer
`INNER JOIN` returns rows where matching records exist in both joined tables.

## Question 020

### Question
What is LEFT JOIN?

### Answer
`LEFT JOIN` returns all rows from the left table and matching rows from the right table. If no match exists, right-side columns are NULL.

## Question 021

### Question
What is RIGHT JOIN?

### Answer
`RIGHT JOIN` returns all rows from the right table and matching rows from the left table. It can usually be rewritten as a LEFT JOIN by switching table order.

## Question 022

### Question
What is FULL OUTER JOIN?

### Answer
`FULL OUTER JOIN` returns matching rows from both tables and unmatched rows from both sides.

## Question 023

### Question
What is CROSS JOIN?

### Answer
`CROSS JOIN` returns the Cartesian product of two tables.

## Question 024

### Question
What is an aggregate function?

### Answer
An aggregate function calculates a single result from multiple rows. Examples are `COUNT`, `SUM`, `AVG`, `MIN`, and `MAX`.

## Question 025

### Question
What does COUNT(*) do?

### Answer
`COUNT(*)` counts rows, including rows where some columns contain NULL.

## Question 026

### Question
What does COUNT(column) do?

### Answer
`COUNT(column)` counts non-NULL values in that column.

## Question 027

### Question
What is DISTINCT?

### Answer
`DISTINCT` removes duplicate rows from the result set.

## Question 028

### Question
What is LIKE?

### Answer
`LIKE` performs pattern matching, often with `%` and `_` wildcards.

## Question 029

### Question
What is IN?

### Answer
`IN` checks whether a value matches one value from a list or subquery.

## Question 030

### Question
What is BETWEEN?

### Answer
`BETWEEN` checks whether a value falls within an inclusive range.

## Question 031

### Question
What is a subquery?

### Answer
A subquery is a query nested inside another query.

## Question 032

### Question
What is a view?

### Answer
A view is a stored query that can be used like a virtual table.

## Question 033

### Question
What is an index?

### Answer
An index is a database structure that speeds up data lookup at the cost of extra storage and slower writes.

## Question 034

### Question
Why not index every column?

### Answer
Indexes consume storage, slow down inserts and updates, and must be maintained by the database. Too many indexes can hurt performance.

## Question 035

### Question
What is a unique constraint?

### Answer
A unique constraint ensures that values in a column or group of columns are unique.

## Question 036

### Question
What is a check constraint?

### Answer
A check constraint enforces a condition on column values.

## Question 037

### Question
What is a default constraint?

### Answer
A default constraint supplies a default value when no value is provided.

## Question 038

### Question
What is normalization?

### Answer
Normalization organizes data to reduce duplication and improve consistency.

## Question 039

### Question
What is denormalization?

### Answer
Denormalization intentionally duplicates or precomputes data to improve read performance or simplify queries.

## Question 040

### Question
What is a transaction?

### Answer
A transaction is a group of operations treated as a single unit of work. It should either complete fully or roll back.

## Question 041

### Question
What does ACID mean?

### Answer
ACID means Atomicity, Consistency, Isolation, and Durability. These are core properties of reliable transactions.

## Question 042

### Question
What is COMMIT?

### Answer
`COMMIT` makes transaction changes permanent.

## Question 043

### Question
What is ROLLBACK?

### Answer
`ROLLBACK` undoes changes made in the current transaction.

## Question 044

### Question
What is a stored procedure?

### Answer
A stored procedure is a saved database routine that can contain SQL statements and procedural logic.

## Question 045

### Question
What is a parameterized query?

### Answer
A parameterized query uses placeholders for values instead of concatenating strings. It improves safety and prevents many SQL injection attacks.

## Question 046

### Question
What is SQL injection?

### Answer
SQL injection is a vulnerability where untrusted input changes the structure of a SQL command. It can expose, modify, or delete data.

## Question 047

### Question
How do you prevent SQL injection?

### Answer
Use parameterized queries, avoid string concatenation for SQL, validate input, and restrict database permissions.

## Question 048

### Question
What is a data type in SQL?

### Answer
A data type defines what kind of value a column can store, such as integer, text, date, decimal, or boolean-like values.

## Question 049

### Question
What is VARCHAR?

### Answer
`VARCHAR` stores variable-length character data.

## Question 050

### Question
What is NVARCHAR?

### Answer
`NVARCHAR` stores Unicode variable-length character data, commonly used when international text is needed.

## Question 051

### Question
What is DECIMAL?

### Answer
`DECIMAL` stores exact numeric values and is commonly used for money-like calculations where floating-point rounding is unacceptable.

## Question 052

### Question
What is DATETIME?

### Answer
`DATETIME` stores date and time values. Exact behavior depends on the database system.

## Question 053

### Question
What is a schema?

### Answer
A schema is a namespace or logical container for database objects such as tables, views, and procedures.

## Question 054

### Question
What is a database migration?

### Answer
A migration is a controlled change to database schema or data, usually versioned with application code.

## Question 055

### Question
What is referential integrity?

### Answer
Referential integrity ensures relationships between tables remain valid, usually through foreign key constraints.

## Question 056

### Question
What is cascading delete?

### Answer
Cascading delete automatically deletes related child rows when a parent row is deleted.

## Question 057

### Question
Why can cascading delete be dangerous?

### Answer
It may delete more data than expected if relationships are not carefully designed.

## Question 058

### Question
What is an alias in SQL?

### Answer
An alias gives a temporary name to a table or column in a query.

## Question 059

### Question
What is SELECT * and why can it be bad?

### Answer
`SELECT *` returns all columns. It can be bad because it transfers unnecessary data, breaks when schemas change, and makes intent unclear.

## Question 060

### Question
What is a many-to-many relationship?

### Answer
A many-to-many relationship means many rows in one table can relate to many rows in another. It is usually modeled with a junction table.

## Question 061

### Question
What is a junction table?

### Answer
A junction table connects two tables in a many-to-many relationship, often containing two foreign keys.

## Question 062

### Question
What is one-to-many relationship?

### Answer
One row in a parent table can relate to many rows in a child table.

## Question 063

### Question
What is one-to-one relationship?

### Answer
One row in a table corresponds to at most one row in another table.

## Question 064

### Question
What is a natural key?

### Answer
A natural key uses real-world data as an identifier, such as an email or national ID.

## Question 065

### Question
What is a surrogate key?

### Answer
A surrogate key is an artificial identifier, often an auto-incrementing integer or GUID.

## Question 066

### Question
What is identity column?

### Answer
An identity column automatically generates numeric values for new rows.

## Question 067

### Question
What is a GUID?

### Answer
A GUID is a globally unique identifier. It is useful for distributed ID generation but can affect index fragmentation depending on usage.

## Question 068

### Question
What is pagination?

### Answer
Pagination returns a subset of rows, usually for displaying one page of results.

## Question 069

### Question
How can pagination be implemented?

### Answer
Common approaches include `OFFSET/FETCH`, `LIMIT/OFFSET`, and keyset pagination.

## Question 070

### Question
What is a duplicate row?

### Answer
A duplicate row has the same relevant values as another row. Whether it is a real duplicate depends on business rules.

## Question 071

### Question
How do you remove duplicates?

### Answer
Use constraints to prevent them. For cleanup, use grouping, window functions, or database-specific deletion strategies.

## Question 072

### Question
What is a temporary table?

### Answer
A temporary table stores intermediate data for a session or transaction, depending on the database.

## Question 073

### Question
What is a CTE?

### Answer
A Common Table Expression defines a named temporary result set for use within a query.

## Question 074

### Question
Why use a CTE?

### Answer
A CTE can improve query readability, structure recursive queries, or separate complex logic into named parts.

## Question 075

### Question
What is UNION?

### Answer
`UNION` combines result sets and removes duplicates.

## Question 076

### Question
What is UNION ALL?

### Answer
`UNION ALL` combines result sets without removing duplicates and is usually faster than `UNION`.

## Question 077

### Question
What is a database backup?

### Answer
A backup is a copy of database data used for recovery after failure, corruption, or accidental deletion.

## Question 078

### Question
What is restore?

### Answer
Restore loads data from a backup into a database system.

## Question 079

### Question
What is an execution plan?

### Answer
An execution plan describes how the database engine intends to execute a query.

## Question 080

### Question
Why should developers understand execution plans?

### Answer
Execution plans help diagnose slow queries, missing indexes, bad joins, and inefficient scans.

## Question 081

### Question
What is table scan?

### Answer
A table scan reads all rows in a table. It can be acceptable for small tables but expensive for large ones.

## Question 082

### Question
What is index seek?

### Answer
An index seek uses an index to locate matching rows efficiently.

## Question 083

### Question
What is index scan?

### Answer
An index scan reads many or all entries in an index.

## Question 084

### Question
What is a composite index?

### Answer
A composite index contains multiple columns. Column order matters for how it can be used.

## Question 085

### Question
What is database connection?

### Answer
A database connection is a communication channel between an application and a database server.

## Question 086

### Question
What is connection pooling?

### Answer
Connection pooling reuses database connections to reduce the overhead of opening new connections.

## Question 087

### Question
Why should applications close/dispose database connections?

### Answer
Closing or disposing returns the connection to the pool and prevents resource exhaustion.

## Question 088

### Question
What is ORM?

### Answer
An Object-Relational Mapper maps database tables and queries to objects in application code.

## Question 089

### Question
What are common ORM risks?

### Answer
ORMs can hide inefficient queries, cause N+1 problems, load too much data, or make database behavior less explicit.

## Question 090

### Question
What is N+1 query problem?

### Answer
N+1 happens when code loads a list and then executes one additional query per item, causing many unnecessary database calls.

## Question 091

### Question
How do you avoid N+1?

### Answer
Use joins, eager loading, projections, batching, or carefully designed queries.

## Question 092

### Question
What is a query parameter?

### Answer
A query parameter is a value supplied separately from SQL text, allowing safe and reusable SQL execution.

## Question 093

### Question
What should a junior developer know about SQL before production work?

### Answer
A junior should understand SELECT, joins, filtering, grouping, transactions, keys, constraints, indexes, parameterized queries, and how to avoid obviously inefficient or unsafe queries.
