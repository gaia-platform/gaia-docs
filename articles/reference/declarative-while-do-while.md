---
author: Don Glover
owner: 
lastupdate: 09/21/2021
---

---

**NOTE**

The information contained in this document represents information about preview features of the product. Features might change when the product is released for general availability.

---

# while and do while

The while and do while statements allow you to iterate over a set of rows in the database.

`while (Table->Table1) { …}`

`do {...} while(Table->Table1)`

## Remarks

The navigation path does not guarantee the order of the returned results.

Tags and navigation paths and tags in while loops:

- References or navigation paths that result in an iteration are not allowed in the condition of a while expression. 
- Tag declarations are not allowed in the while loop.  
- It is permissible to use a tag that is already declared in an enclosing scope.

**Note**: Labels on `while()`, `do-while()` statements are not supported.