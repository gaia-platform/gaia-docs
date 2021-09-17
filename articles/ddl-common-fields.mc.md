---
author: 
owner: 
lastupdate: 
---

# Common Fields

`field_name references table_name [using [table_name].field_name]`

The using is optional. It is only needed when you have more than one relationship between the same two tables.

## Forward References

Forward References allow an identifier to reference an entity whose definition appears after the identifier. In other words, the order of the data definition statements does not matter. 

In Gaia DDL you build a statement that includes Forward References by including all of the statement segments in a single statement terminated by a semicolon. For example, the following DDL segment defines two tables: doctor and patient. The doctor table creates a reference to the patient table, which is defined after the doctor table.

```
create table doctor (
 name string,
 patients references patient[]
)
create table patient (
 name string,
 doctor references doctor.patients
);
```

If the doctor table definition were terminated with a semicolon the compilation of the DDL would fail since the patient table has not yet been defined.
