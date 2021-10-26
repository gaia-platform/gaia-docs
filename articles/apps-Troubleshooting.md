---
author: Don Glover
owner: 
lastupdate: 10/26/2021
---

# Troubleshooting

Cannot delete object with ID '..', type '...', because it is still referenced by another object with ID '..', type '...' This error occurs when you try to delete an object that is still in a relationship (see Delete objects in 1:n/1:1 relationships).

An example of the message could be: Cannot delete object with ID' 98', type '4211092936', because it is still referenced by another object with ID' 101', type '40335167'. What you need to do is to look up what types are '4211092936' and '40335167' using gaiac:

```bash
gaiac -i
```
<pre>
> \l
+-----------+-------------------+----+------------+
| Database | Name | ID | Type                     |
+-----------+-------------------+----+------------+
...
+-----------+-------------------+----+------------+
| hospital  | doctor            | 59 | 4211092936 |
+-----------+-------------------+----+------------+
| hospital  | patient           | 61 | 40335167   |
+-----------+-------------------+----+------------+
| hospital  | address           | 65 | 1084090863 |
+-----------+-------------------+----+------------+
</pre>

* 4211092936 → doctor
* 40335167 → patient

This error indicates that somewhere you are trying to delete an object of type doctor that still references a patient. You need to look for that code and explicitly disconnect the doctor object from the patient.
