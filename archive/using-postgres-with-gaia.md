---
author: Don Glover
owner: Don Glover
lastupdate: 
---


# Using Postgres to access Gaia database information

For information on how to setup and configure Postgres for use with the Gaia Foreign Data Wrapper (FDW), see [PostgreSQL Downloads](https://www.postgresql.org/download/).

Connect to Postgres using the Postgres account credentials:

`sudo -u postgres psql`

Execute the following commands in order, at the Postgres prompt:

```sql
DROP DATABASE IF EXISTS hello;
CREATE DATABASE hello;

\c hello;

CREATE EXTENSION gaia_fdw;
CREATE SERVER gaia FOREIGN DATA WRAPPER gaia_fdw;
CREATE SCHEMA hello_fdw;
IMPORT FOREIGN SCHEMA hello_fdw
	FROM SERVER gaia INTO hello_fdw;
```

We create a hello database, to enable faster cleanup through a DROP DATABASE call later.

The last 4 commands after connecting to the database enable Postgres to use the Gaia FDW to map the Gaia database tables to hello database foreign tables.

This enables us to now query the Gaia database tables directly:

```sql
select * from hello_fdw.names;
select * from hello_fdw.greetings;
```

We can also query Gaia database catalogs. For example, this is how we can query the names of all the Gaia database tables:

```sql
select name from hello_fdw.gaia_table; 
```

To exit the Postgres session, use the following command:

**\q**