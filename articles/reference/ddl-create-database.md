---
author: 
owner: 
lastupdate: 
---

---

**NOTE**

The information contained in this document represents information about preview features of the product. Features might change when the product is released for general availability.

---
# create database

Optional. Creates a new database. If the database already exists, gaiac returns an error and the database is not created. 

`[create] database [if not exists] database_name;`

The create keyword is optional.

The `if not exists` keyword is optional. If a database with the same name already exists, the command is ignored. However, there is no verification that the existing database has a structure identical to the one specified by the create database statement.

## Remarks

The `create database` statement automatically switches you to the new database. There is no need to invoke `use` if the intention is to create tables in the database that was just created. 

The short form of the `create database` statement, in which you omit the create operator, drops the table before attempting to create it and has the same effect as the following:

`drop database if exists database_nam; create database database_name;`

Use the interactive feature of gaiac to list the instantiated databases:

<pre>
gaiac> \ld
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