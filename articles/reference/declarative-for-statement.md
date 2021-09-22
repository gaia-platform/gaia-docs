---
author: Don Glover
owner: 
lastupdate: 09/21/2021
---

---

**NOTE**

The information contained in this document represents information about preview features of the product. Features might change when the product is released for general availability.

---

# for(. . . )

The for statement allows you to iterate over a set of rows in the database:

- for(table) statement: Executes "statement" once for each row in the table.
- for(Table->Table1) statement: executes the statement once for each row in the 1:m list implied by the Navigation Path

The declarative for statement is similar to the range-based for statement in standard C++. 

	 `for(range_declaration : range_expression)`

The for statement in Gaia’s Declarative C++ has the following differences:

- A tag specifies the range_declaration. Unlike the standard C++ for, you can declare multiple tags to allow access to multiple "loop" variables within the scope of the for. 
    - You must specify a tag if you want to reference the rows in the body of the for.
- The range_expression in the for statement is a table or a path to a table.  
- A label can precede the for statement. You can use the label with the continue and break keywords.
- An optional nomatch statement can follow the body of the for. Code in a nomatch clause is executed if nothing in the body of the for loop executes.

The for statement has the general form of:

```
[label:]
for (Table or Navigation Path)
{
  [continue [label]];
  [break [label]];
}
[nomatch
{
}

```

Let’s consider a simple example:
 for(Flight->Segments->Trips->T:Travellers)
  	CheckDocuments(T)

This statement causes the system to check the documents for each passenger on a flight. Notice the use of the T as a tag.

This effectively translates into:

for each Flight
&nbsp;&nbsp;for each Segment in the Flight
&nbsp;&nbsp;&nbsp;&nbsp;for each Trip in the Segment
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;for each Traveler T in the Trip
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;then checkDocument(T)

### for (. . .) if (. . .) {. . .}

Combine the declarative for with an if to perform filtered iteration on the data. For example:

`for(T:Travellers) if (T.age>65) {. . .}`

Fires a rule that applies only to travelers over 65. As before, we can iterate and apply the filter to either an entire table or just to the target of a 1:m relationship.
