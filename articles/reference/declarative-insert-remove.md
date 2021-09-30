---
author: Don Glover
owner: 
lastupdate: 09/21/2021
---

---

**NOTE**

The information contained in this document represents information about preview features of the product. Features might change when the product is released for general availability.

---

# insert and remove

Insert and remove are methods on a table in the Gaia catalog.

**Insert**

Inserts a row into a table.

`[Table_name].Insert(field_1: value_1,..., field_2: value_2)`

Table_name: a table in the Catalog.
The parameters to the insert statement set the fields in a row of the specified table. If you do not specify the value for a field in the table Gaia inserts a default value; 0 for numeric fields and an empty string for string fields.
The insert statement only allows inserting into primitive types.

Example Schema:

```
create table if not exists flight (
    airline string,
    departure_time int64,
    arrival_time int64,
    flight_number int64
);

create table if not exists passenger (
     first_name string,
     last_name string,
     has_ticket boolean,
     flight references flight
);
```

## Example

```
// Note that the following methods do not list all the parameters. 
flight.insert(
    airline: "Alaska",
    flight_number: 23
)

td::string 

auto tengiz = passenger.insert(
    first_name: "Tengiz",
    last_name: "Kharatishvili"
)
```

**remove**

Removes one or more rows from a table.

`[Table_Name].remove()`:  Removes the current row based on the anchor and the reference,

```
on_update(p:passenger)
{
    p.remove();
}
```

**Note**: Attempting to remove a row that is currently connected will result in an error. Call disconnect() first and then remove the row.