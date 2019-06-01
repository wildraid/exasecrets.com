---
layout: post
title: "How to execute multiple SQL statements in single transaction using exajload?"
---

Multiuser compliance of Exasol is ensured by its transaction system. A transaction consists of multiple SQL commands and is either completed with a COMMIT statement or aborted with a ROLLBACK statement.

It is very useful for typical ETL scenarios:

1. Full table reload:
   ```sql
   TRUNCATE TABLE xxx ;                     -- cleanup table
   IMPORT INTO xxx FROM ... ;               -- import fresh data
   COMMIT ;
   ```
2. Incremental updates: 
   ```sql
   DELETE FROM TABLE xxx WHERE dt={date} ;  -- delete existing data for specific day
   IMPORT INTO xxx FROM ... ;               -- import fresh data for specific day
   COMMIT ;
   ```
 
It is easy to do in normal SQL client, but it is not officially documented how to use transactions in context of `exajload` process.

You may do so by adding `;autocommit=0` option to connection string and by adding explicit `-postsql 'COMMIT'`.

### Example

1. Create table:
    ```sql
    CREATE OR REPLACE TABLE users
    (
        user_id         DECIMAL(18,0),
        user_name       VARCHAR(255),
        register_dt     DATE,
        last_visit_ts   TIMESTAMP,
        is_female       BOOLEAN,
        user_rating     DECIMAL(10,5),
        user_score      DOUBLE,
        status          VARCHAR(50)
    );
    ```
2. Download Exasol JDBC driver from [Download section](https://www.exasol.com/portal/display/DOWNLOAD/) and extract it to get `exajload` executable.
3. Download file with test data: [users.csv](/assets/data/users.csv)
4. Import data using following command:

```
./exajload \
-c 'exasol-dev:8563;autocommit=0' \
-u 'SYS' \
-P 'exasol' \
-s 'EXASECRETS' \
-presql 'TRUNCATE TABLE users' \
-sql 'IMPORT INTO users FROM LOCAL CSV FILE '\''users.csv'\'' ROW SEPARATOR = '\''LF'\''' \
-postsql 'COMMIT'
```

You may try to break the IMPORT statement and re-run `exajload` command again. If you check the contents of the table after IMPORT error, you'll see that TRUNCATE statement was rolled back, and data is still there.

You may also use multiple `-presql` and `-postsql` statements to create even more complex ETL tasks running sophisticated transformations in `exajload` context.
