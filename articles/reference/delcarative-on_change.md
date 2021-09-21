---
author: 
owner: 
lastupdate: 
---

# on_change

Reacts to the Insertion or change of a row that contains the specified fields in the specified table. 

Syntax:

`on_change(field1, field2,..., fieldn){ . . . }`

field: A comma separated list of fields in the database to watch for changes.  All fields specified in the must be from the same table.

`on_change(table){ . . . }`

table: Specifies the table to watch for changes in the database.

Remarks

Example
