---
author: Don Glover
owner: 
lastupdate: 09/21/2021
---

---

**NOTE**

The information contained in this document represents information about preview features of the product. Features might change when the product is released for general availability.

---

# on_insert

Specifies that the rule fires on the insertion of a row in the specified table.

Syntax:

`on_insert(table){ . . . }`

table: Specifies the table in the database to watch for insertions.

## Remarks:

Unlike the other On prefixes, on_insert takes a single parameter that must be a table name. 

## Example

```c++
    // Rule 1: Whenever a name is inserted,
    // insert a new greeting into the greetings table.
    on_insert(names)
    {
        // Form the greeting using the name.
        string new_greeting = "Hello " + string(names.name) + "!";

        // Insert the greeting.
        gaia::hello::greetings_t::insert_row(new_greeting.c_str());
    }
```
