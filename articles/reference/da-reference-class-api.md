---
author: 
owner: 
lastupdate: 
---

---

**NOTE**

The information contained in this document represents information about preview features of the product. Features might change when the product is released for general availability.

---

# Reference Class API

Provides access for one-to-one relationships.

Examples in these articles are taken from the Direct Access sample app that is installed with the SDK at /opt/gaia/examples/direct_access.

`class_name_ref_t`

Reference order is determined by the order of the tables in the DDL.

The _ref_t class is only exposed on the first table that specifies the relationship in the DDL. This means that you can change membership of the related records using the first table and not the second. The second table only exposes the table_t type for the 1:1 link and not the table_ref_t type.

Referential order - as a basis to replace parent/child)

Exposes:

**Constructor**

```cpp
    doctor_t(gaia::common::gaia_id_t parent,
            gaia::common::gaia_id_t child,
            gaia::common::reference_offset_t child_offset);
```


**Connect and Disconnect methods**

`bool connect(gaia::common::gaia_id_t id);`

`bool connect(const doctor_t_t& object);`

Links rows between two tables based on an existing relationship between the tables.

`bool disconnect();`

`bool disconnect(common::gaia_id_t id);`

Breaks the connection between rows. 