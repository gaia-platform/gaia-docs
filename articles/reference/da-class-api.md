---
author: 
owner: 
lastupdate: 
---

---

**NOTE**

The information contained in this document represents information about preview features of the product. Features might change when the product is released for general availability.

Examples in these articles are taken from the Direct Access sample app that is installed with the SDK at /opt/gaia/examples/direct_access.

---

# Direct Access Classes API

The Direct Access Classes model the tables in the database.

For each table in the database, gaiac generates code for a Direct Access Class (DAC) that includes the following items.

Accessors: The DAC implements an accessor for each field in the table to retrieve the value of the field. For example:

* `const char* doctor_t::field_name1() const`
* `uint64_t doctor_t::field_name2() const`
* `float doctor_t::field_name3() const`
* `bool doctor_t::field_name4() const`

For information on the full set of supported types, see [create table](ddl-create-table.md).

## constructors

`doctor_t:doctor_t()`

Public default constructor. Creates an empty DAC instance.

It is valid to cache DAC across transaction boundaries. The system guarantees that the correct data is maintained.

## Methods

**insert_row**

`gaia::common::gaia_id_t doctor_t::insert_row(<field 1, <field2>,...)`

Adds a row to the table described by the class.

**gaia_typename**

`const char* doctor_t::gaia_typename()`

Returns the class name *doctor_t*.

**list**

`gaia::direct_access::edc_container_t<c_gaia_type_address, doctor_t doctor_t::list()`

Exposes an iterator over all the records in a table.

## Structure for filtering

Provides predicates that you use to filter rows using the container class predicates.

## Example

It this example doctor is the class name that replaces \[class name\] in the template above.

```c++
 struct expr_ {
     static gaia::direct_access::expression_t\<doctor_t,  gaia::common::gaia_id_t gaia_id;
     static gaia::direct_access::expression_t<doctor_t, const char* street;
     static gaia::direct_access::expression_t<doctor_t, const char* city;
     static gaia::direct_access::expression_t<doctor_t, patient_t patient;
 };
```

## Additional Methods

In addition, the DAC exposes additional methods that enable you to work with table data.

**writer**

`doctor_t writer();`

Returns a reference that is pre-populated with values from the row.

**gaia_type**

`gaia::common::gaia_type_t gaia_type() override;`

Returns the type id for the DAC.

**get**

`static T_gaia get(gaia::common::gaia_id_t id);`

Retrieves a specific object of the type `T_gaia` based on its `ID`. An exception is thrown if the object represented by the id is not of type `T_gaia`.

@param id the `gaia_id_t` of a specific database object, of type `container_type_id`.

**delete_row**

`void delete_row();`

Delete the database object. This doesn\'t destroy the extended data class object.

`static void delete_row(gaia::common::gaia_id_t id);`

Delete the database object specified by the id.

## Writer Class API

The writer class provides methods that allow you to add, update, and delete rows from DAC.

Code is not generated for the following exposed methods:

**delete_row**

`doctor_t::delete_row()`

Deletes the current row pointed to by the Direct Access `doctor_t` object.

```c++
for (auto doctor_it = doctor_t::list().begin();
doctor_it != doctor_t::list().end();)
{
    auto next_doctor_it = doctor_it++;
    next_doctor_it->delete_row();
}
```

**doctor_writer** (as a typedef in the generated code)

Includes setters for each field in the class.

**update_row**

`doctor_writer::update_row`

Updates current row pointed to by a DA _t object.

**insert_row**

`doctor_writer::insert_row`

Adds a new row in a Direct Access \*class name\*_t object.


## Example

The following code snippet inserts a new patient and then update his information after the row was inserted.

```c++
gaia_id_t id = patient_t::insert_row("John", 175, false, nullptr, {});
patient_t john = patient_t::get(id);
gaia_log::app().info("Patient name is: {}", john.name());

// Obtain the writer object for an existing record.
patient_writer john_w = john.writer();
john_w.name = "Jane";
john_w.height = 178;
john_w.analysis_results = {1.0, 1.2, 0.3};
john_w.is_active = true;
john_w.update_row();
gaia_log::app().info("Patient name now is: {}", john.name());
```