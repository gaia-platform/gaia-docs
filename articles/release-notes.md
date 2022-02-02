---
author: Don Glover
owner: 
lastupdate: 
---

# Release Notes

## Version 0.4.0.0-beta 02/07/2022

Added C++ 17 style optional value support allowing for null values.

Added  Scalar array support for declarative language (Gregory)

Improvements in rule translation to make writing rules more intuitive

Relaxed referential integrity restraints to allow the deletion of connected nodes.

The delete_row public API  now accepts an additional optional parameter to force the deletion of connected parent nodes.

if catalog encounters a fatal exception, gaiat now emits single diagnostic rather than a full dump of internal gaiat errors.

Refactored expressions into their own namespace.

Fixed and error where using #define in a rule caused a compilation error.

Changed the parameter name for `autotransaction_t` from `auto_begin` to `auto_restart`.

## Version 0.3.1.0-beta 10/15/2021

The drop table command, in both it's long and short form does not remove any existing records from the database.

The database will be in an inconsistent state since the catalog entries will not match that data in the database and any data operation can fail or have unintended results.

Before dropping a table, you should first programmatically remove all records from the table.