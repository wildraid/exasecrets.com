### Exasol in virtual environment

It's a good idea to setup a separate Exasol instance for tests in virtual environment. You may run it on your local laptop, remote server or in the cloud.

There are two possible installation options available:

1. Community edition ([official guide](https://docs.exasol.com/get_started/tryexasol/communityedition.htm))
2. Exasol for Docker ([github](https://github.com/exasol/docker-db))

Please note that Docker version only works on Linux at this moment. Docker for MacOS and Docker for Windows are not supported.

### Testing schema

I use schema called `EXASECRETS` for tests and code samples provided in this blog.

In order to create this schema, please run following SQL statement:

```sql
CREATE SCHEMA EXASECRETS;
```

### Credentials

For testing purposes I assume that Exasol instance has following connection string and credentials:

```
dsn       = localhost:8563
user      = SYS
password  = exasol
schema    = EXASECRETS
```

### PyEXASOL

Some examples involving Python might use [PyEXASOL](https://github.com/badoo/pyexasol) package. Please install it using following simple command:

```
pip install pyexasol
```
