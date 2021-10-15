---
author: Don Glover
owner: 
lastupdate: 
---

# Release Notes

## Version 0.3.1.0-beta 10/15/2021

The drop table command, in both it's long and short form does not remove any existing records from the database.

The database will be in an inconsistent state since the catalog entries will not match that data in the database and any data operation can fail or have unintended results.

Before dropping a table, you should first programmatically remove all records from the table.