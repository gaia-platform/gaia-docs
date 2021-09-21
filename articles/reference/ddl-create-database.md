---
author: 
owner: 
lastupdate: 
---

# create database

Optional. Creates a new database. If the database already exists, gaiac returns an error and the database is not created. 

`[create] database [if not exists] database_name;`

The create keyword is optional.

The `if not exists` operator is optional. If a database with the same name already exists, the command is ignored. However, there is no verification that the existing database has a structure identical to the one specified by the create database statement.

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