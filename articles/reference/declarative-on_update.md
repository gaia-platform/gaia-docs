---
author: Don Glover
owner: 
lastupdate: 09/21/2021
---

---

**NOTE**

The information contained in this document represents information about preview features of the product. Features might change when the product is released for general availability.

---

# on_update

Specifies that the rule fires when a change occurs to the specified fields in existing rows in a table or a change to a specified table.

Syntax:

`on_update(Field names){ . . . }`

Field Names: A list of comma separated field names in the format: field1, field2, …, fieldn.

`on_update(table name){ . . . }`

table name: The name of the table.  If any field  in the table changes, Gaia fires the rule.

Remarks

If the fields are unique in the Catalog, you can omit specifying which table they are in.

Example
