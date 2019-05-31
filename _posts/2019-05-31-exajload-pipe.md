---
layout: post
title: "How to import data from pipe using exajload?"
---

Official documentation of `exajload` utility only mention IMPORT from various files. However, it is possible to IMPORT not only from files, but also from standard input `STDIN`.

It might be useful while building complex ETL pipelines and applying dynamic transformations before IMPORT.

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
cat users.csv | ./exajload \
-c 'localhost:8563' \
-u 'SYS' \
-P 'exasol' \
-s 'EXASECRETS' \
-presql 'TRUNCATE TABLE users' \
-sql 'IMPORT INTO users FROM LOCAL CSV FILE '\''/dev/stdin'\'' ROW SEPARATOR = '\''LF'\'''
```

You may use `/dev/stdin` special value to read from `STDIN` instead of normal file.

### Compression with pipes

It is also possible to use compression while importing from `STDIN`. In order to make it work, we have add extension `.gz`, `.bzip2` or `.zip` to FILE value.

You may do it using a simple trick with symlinks:

```
ln -s /dev/stdin stdin.gz
```

Now you have a pseudo-file called `stdin.gz`, which can be used as valid FILE value for exajload.

Let's try to compress data file on the fly and IMPORT it as gzip-encoded stream:

```
gzip -c users.csv | ./exajload \
-c 'localhost:8563' \
-u 'SYS' \
-P 'exasol' \
-s 'EXASECRETS' \
-presql 'TRUNCATE TABLE users' \
-sql 'IMPORT INTO users FROM LOCAL CSV FILE '\''stdin.gz'\'' ROW SEPARATOR = '\''LF'\'''
```

This technique might help you to reduce the amount of traffic transferred over network and save CPU resources by preventing unnecessary decompression.
