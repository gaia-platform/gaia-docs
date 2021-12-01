---
author: 
owner: 
lastupdate: 
---

---

**NOTE**

The information contained in this document represents information about preview features of the product. Features might change when the product is released for general availability.

---

# Exceptions

`edc_invalid_member`

A child's parent pointer must match the parent record.

`edc_inconsistent_list`

The child refers to a parent but is not found in that parent's list.

`edc_invalid_state`

To connect two objects, a `gaia_id` is needed but not available until create is called during the `insert_row()`.

`edc_already_inserted`

An attempt was made to insert a member that has already been inserted.
