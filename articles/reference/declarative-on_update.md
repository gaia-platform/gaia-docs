---
author: Don Glover
owner: 
lastupdate: 09/21/2021
---

---

**NOTE**

The information contained in this document represents information about preview features of the product. Features might change when the product is released for general availability.

---

# on_update

Specifies that the rule fires when a change occurs to the specified fields in existing rows in a table or a change to a specified table.

Syntax:

`on_update(Field names){ . . . }`

Field Names: A list of comma separated field names in the format: field1, field2, …, fieldn.

`on_update(table name){ . . . }`

table name: The name of the table.  If any field  in the table changes, Gaia fires the rule.

### Remarks

If the fields are unique in the Catalog, you can omit specifying which table they are in.

### Example

```c++

    // Rule 2:  Verify the temperature is kept in range if the
    // incubator temperature limits change. 
    on_update(incubator.max_temp, incubator.min_temp)
    {
        if (!incubator.is_on)
        {
            return;
        }

        sensor_loop:
        for (S:sensor)
        {
            if (S.value < min_temp 
                || S.value > max_temp)
            {
                for (A:actuator)
                {
                    A.value = adjust_temperature(min_temp, max_temp, S.value, A.value);
                    A.timestamp = g_timestamp;
                }
                break sensor_loop;
            }
        }
    }
```
