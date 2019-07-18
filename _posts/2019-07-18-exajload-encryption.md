---
layout: post
title: "How to disable encryption and improve performance of exajload?"
---

Starting from Exasol version 6.0 encryption is enabled in JDBC and ODBC drivers by default. Most people are not aware of this change and how it applies to `exajload`. All traffic from `exajload` process to Exasol node is encrypted by default.

It is possible to disable encryption by adding option `;encryption=0` to connection string. It is strongly advised to do so if you're using a secured tunnel already, or if security is not a concern.

This change may largely improve performance of ETL jobs, but actual results may vary depending on CPU and network setup.

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
4. Import data with encryption being enabled (default):
    ```
    time ./exajload \
    -c 'exasol-dev:8563;encryption=1' \
    -u 'SYS' \
    -P 'exasol' \
    -s 'EXASECRETS' \
    -presql 'TRUNCATE TABLE users' \
    -sql 'IMPORT INTO users FROM LOCAL CSV FILE '\''users.csv'\'' ROW SEPARATOR = '\''LF'\'''
    ```
5. Import data with encryption being disabled:
    ```
    time ./exajload \
    -c 'exasol-dev:8563;encryption=0' \
    -u 'SYS' \
    -P 'exasol' \
    -s 'EXASECRETS' \
    -presql 'TRUNCATE TABLE users' \
    -sql 'IMPORT INTO users FROM LOCAL CSV FILE '\''users.csv'\'' ROW SEPARATOR = '\''LF'\'''
    ```
6. Compare execution time.
7. Try to do the same test using your production data set.
