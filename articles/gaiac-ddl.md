---
author: Don Glover
owner: Don Glover
lastupdate: 05/28/2021
---

# DDL

---

**NOTE**

The information contained in this document represents information about prerelease features of the product. Features might change when the product is released for general availability.

---

## Data Definition Language

The Gaia DDL is similar to SQL since many of the platform features, such as tables, index, and constraints, map naturally to the similar or equivalent relational database features.

**Note**: Gaia events have a 64KB (-16bytes) payload limit.

### CREATE DATABASE

Creates a new database. If the database already exists, gaiac returns an error and the database is not created.

```sql
CREATE DATABASE [if not exits] database_name;
```

Use the optional `if not exists' to prevents an error from occurring if the database exists. If a database with the same name already exists, the command is ignored. However, there is no verification that the existing database has a structure identical to that indicated by the CREATE DATABASE statement.

Use the interactive feature of gaiac to list the instantiated databases:

gaiac> \ld

<pre>
+-----------+-----+
| Name      | ID  |
+-----------+-----+
| catalog   | 1   |
+-----------+-----+
| event_log | 40  |
+-----------+-----+
| hello     | 125 |
+-----------+-----+
| flights   | 140 |
+-----------+-----+
</pre>

### DROP DATABASE

Removes the database from the catalog.

```sql
DROP DATABASE database_name;
```

### USE

Specifies the database to use when compiling the DDL.

```sql
USE database_name;
```

### CREATE TABLE

Creates new a table. If the table already exists, gaiac returns an error and the table is not created.

```sql
CREATE TABLE [if not exists] table_name ( [
{ fieldname datatype
[, ... ]
] );
```

Use the optional `if not exists' to prevents an error from occurring if the table exists. If a table with the same name already exists, the command is ignored. However, there is no verification that the existing table has a structure identical to that indicated by the CREATE TABLE statement.

The following example creates a table named "department."

```sql
CREATE TABLE if not exists department ( name string, current bool active);
```

Active Fields are Database Column names that you refer to in the body of a Declarative Rule expression. When your declarative code refers to one of these Fields with a read operation, Gaia fires an Event that schedules the associated Declarative Rules to execute.

Use the interactive feature of gaiac to list the instantiated tables:

gaiac> \lt
<pre>
+-----------+-------------------+-----+------------+
| Database  | Name              | ID  | Type       |
+-----------+-------------------+-----+------------+
| catalog   | gaia_database     | 2   | 4294967291 |
+-----------+-------------------+-----+------------+
| catalog   | gaia_table        | 4   | 4294967294 |
+-----------+-------------------+-----+------------+
| catalog   | gaia_field        | 11  | 4294967295 |
+-----------+-------------------+-----+------------+
| catalog   | gaia_relationship | 19  | 4294967290 |
+-----------+-------------------+-----+------------+
| catalog   | gaia_ruleset      | 31  | 4294967292 |
+-----------+-------------------+-----+------------+
| catalog   | gaia_rule         | 37  | 4294967293 |
+-----------+-------------------+-----+------------+
| event_log | event_log         | 41  | 4294963200 |
+-----------+-------------------+-----+------------+
| hello     | names             | 126 | 9          |
+-----------+-------------------+-----+------------+
| hello     | greetings         | 128 | 10         |
+-----------+-------------------+-----+------------+
| hello     | another_table     | 131 | 11         |
+-----------+-------------------+-----+------------+
| catalog   | gaia_index        | 134 | 4294967289 |
+-----------+-------------------+-----+------------+
| flights   | airplane          | 141 | 9          |
+-----------+-------------------+-----+------------+
| flights   | route             | 144 | 10         |
+-----------+-------------------+-----+------------+
| flights   | flight            | 147 | 11         |
+-----------+-------------------+-----+------------+
| flights   | segment           | 154 | 12         |
+-----------+-------------------+-----+------------+
| flights   | trip              | 158 | 13         |
+-----------+-------------------+-----+------------+
| flights   | traveller         | 161 | 14         |
+-----------+-------------------+-----+------------+
</pre>

### DROP TABLE

Removes the table from the catalog.

```sql
DROP TABLE table_name;
```

### RELATIONSHIPS

Creates a one to many link between tables.

```sql
CREATE RELATIONSHIPS [if not exists] relationship_name (
    table_name_1.field_name_1 -> table_name_2[],
    table_name_2.field_name_2 -> table_name_1
);
```

The following examples creates relationships  named "employee." The "department" field has a foreign key constraint pointing to the department.

```sql
create relationship if not exists incubator_sensors (
    incubator.sensors -> sensor[],
    sensor.incubator -> incubator
);

create relationship if not exists incubator_actuators (
    incubator.actuators -> actuator[],
    actuator.incubator -> incubator
);
```

Use the interactive feature of gaiac to list the instantiated relationships:

gaiac> \lr
<pre>
+----------------------------------+--------------------------+--------------------------------------------+----+
| Name                             | Parent (link)            | Child (link)                               | ID |
+----------------------------------+--------------------------+--------------------------------------------+----+
| gaia_catalog_database_table      | gaia_database (database) | gaia_table (gaia_tables)                   | 10 |
+----------------------------------+--------------------------+--------------------------------------------+----+
| gaia_catalog_table_field         | gaia_table (table)       | gaia_field (gaia_fields)                   | 19 |
+----------------------------------+--------------------------+--------------------------------------------+----+
| gaia_catalog_relationship_parent | gaia_table (parent)      | gaia_relationship (outgoing_relationships) | 32 |
+----------------------------------+--------------------------+--------------------------------------------+----+
| gaia_catalog_relationship_child  | gaia_table (child)       | gaia_relationship (incoming_relationships) | 33 |
+----------------------------------+--------------------------+--------------------------------------------+----+
| gaia_catalog_ruleset_rule        | gaia_ruleset (ruleset)   | gaia_rule (gaia_rules)                     | 42 |
+----------------------------------+--------------------------+--------------------------------------------+----+
| gaia_catalog_table_index         | gaia_table (table)       | gaia_index (gaia_indexes)                  | 48 |
+----------------------------------+--------------------------+--------------------------------------------+----+
| incubator_sensors                | incubator (incubator)    | sensor (sensors)                           | 72 |
+----------------------------------+--------------------------+--------------------------------------------+----+
| incubator_actuators              | incubator (incubator)    | actuator (actuators)                       | 73 |
+----------------------------------+--------------------------+--------------------------------------------+----+
</pre>

## Example DDL

```sql
---------------------------------------------
-- Copyright (c) Gaia Platform LLC
-- All rights reserved.
---------------------------------------------

create database if not exists incubator;

use incubator;

create table if not exists incubator (
      name string,
      is_on bool,
      min_temp float,
      max_temp float
);

create table if not exists sensor (
      name string,
      timestamp uint64,
      value float
);

create table if not exists actuator (
      name string,
      timestamp uint64,
      value float
);

create relationship if not exists incubator_sensors (
    incubator.sensors -> sensor[],
    sensor.incubator -> incubator
);

create relationship if not exists incubator_actuators (
    incubator.actuators -> actuator[],
    actuator.incubator -> incubator
);
```