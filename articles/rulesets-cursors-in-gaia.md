---
author: Don Glover
owner: Don Glover
lastupdate: 05/28/2021
---

---

**NOTE**

The information contained in this document represents information about preview features of the product. Features might change when the product is released for general availability.

---
# Cursors in Gaia

When getting a list of records from the database, Gaia gets a dynamic cursor to access the data. This means that all changes to the data are immediately visible to the transaction making the changes.

The current Rule has a 'live view' of the data to process based on the data in fields that are being modified and the declarative language operates as if the Rule is operating on 'local data'.

When writing Rules, be aware that when the execution of the Rule adds new rows to that list, these changes are also seen 'live' in the results coming back from the database. Situations can occur where you are adding rows that are then processed resulting in adding rows and your app can enter an endless loop of processing data.
