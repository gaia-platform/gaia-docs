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

`*class name*_ref_t`

Reference order is (currently) determined by the order of the tables in the DDL.

(New Term? Referential order - as a basis to replace parent/child)

Exposes:

**Constructor**

    ```
        *class name*_t(gaia::common::gaia_id_t parent,
                gaia::common::gaia_id_t child,
                gaia::common::reference_offset_t child_offset);
    ```


**Connect and Disconnect methods**

`bool connect(gaia::common::gaia_id_t id);`

`bool connect(const \*class name\* \_t& object);`

Links rows between two tables based on an existing relationship between
the tables.

`bool disconnect();`

`bool disconnect(common::gaia_id_t id);`

Breaks the connection between rows. 