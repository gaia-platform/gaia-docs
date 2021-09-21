---
author: 
owner: 
lastupdate: 
---

# on_insert

Reacts to the insertion of a new row.

Syntax:

`on_insert(table){ . . . }`

table: Specifies the table in the database to watch for insertions.

### Remarks:

Unlike the other On prefixes, on_insert takes a single parameter that must be a table name. 

Example:

```
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
