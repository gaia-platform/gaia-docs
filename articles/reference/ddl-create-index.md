---
author: 
owner: 
lastupdate: 
---

# create index

Creates an index that you can use as a field constraint.

`create unique hash index index_name on table_name(field_name?)`

Using the `unique` keyword specifies that the field can only contain unique values. The index name serves as the constraint name.

## Unique constraints

A unique constraint on a table field is defined when creating tables using the following syntax.

```
CREATE TABLE flight (
  number INT32 UNIQUE,
  miles_flown INT32
);

CREATE TABLE flight (
    number INT32 CONSTRAINT flight_number_idx UNIQUE,
    miles_flown INT32
);
```

Using the `unique` keyword in the table creation statement is the preferred method to define uniqueness. 
