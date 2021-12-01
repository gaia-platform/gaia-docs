---
author: 
owner: 
lastupdate: 
---

---

**NOTE**

The information contained in this document represents information about preview features of the product. Features might change when the product is released for general availability.

---

# Direct Access API overview

The Direct Access Classes (DAC) provide an API to work directly with the Gaia database. You use [gaiac](../tools/tool-gaiac.md) to read a schema from the database and generate the headers that contain the API that you use to access data.

Examples in these articles are taken from the Direct Access sample app that is installed with the SDK at /opt/gaia/examples/direct_access.

For a discussion of the sample, see [Using the Direct Access
Classes](../apps-direct-access.md).

There are four classes that you use when writing Direct Access code:

* DA class (\*class name\*_t)
  * There is one class for each table defined in the database schema.

In addition to the DA classes, there are three other classes that are
defined that you will use when writing DA code:

* writer (\*class name\*_writer)
* reference (\*class name\*_ref_t) -> for 1:1 relationships
* container (\*class name\*_list_t) -> for 1:n relationships

 The \*class name\* for each of these class types is the table name listed in the schema.

For example, for the following table definition:

```c++
 table doctor (
  name string,
  email string unique,
  patients references patient[]
 )
```

 The following classes are generated:

* Direct Access Class: `doctor_t`
* writer class: `doctor_writer`
* Container class: `doctor_t::patients_list_t`

To simplify the explanations of the generated classes, the articles in this section will refer to a specific instance of a generated class, address, doctor, or patient as defined in the DDL in the Direct Access sample.
