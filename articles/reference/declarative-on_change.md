---
author: Don Glover
owner: 
lastupdate: 09/21/2021
---

---

**NOTE**

The information contained in this document represents information about preview features of the product. Features might change when the product is released for general availability.

---

# on_change

Specifies that the rule fires when there is an insertion or change of a row that contains the specified fields in the specified table. 

Syntax:

`on_change(field1, field2,..., fieldn){ . . . }`

field: A comma separated list of fields in the database to watch for changes.  All fields specified in the must be from the same table.

`on_change(table){ . . . }`

table: Specifies the table to watch for changes in the database.
