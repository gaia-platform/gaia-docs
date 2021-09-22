---
author: Don Glover
owner: 
lastupdate: 09/21/2021
---

---

**NOTE**

The information contained in this document represents information about preview features of the product. Features might change when the product is released for general availability.

---

# if Statement

The declarative `if` statement is very similar to the C++ `if` statement with two important exceptions.  

In Gaia a navigation path can return a set of rows over which the `if` statement will iterate. You can use the break and continue statements to control how iteration proceeds.

A `nomatch` clause is added to indicate when the condition in an `if` or `else-if` could not be evaluated if there are no rows returned in the navigation path. If the `nomatch` clause is omitted and there are no rows, then execution continues with the next statement after the `if`, as if `nomatch {}` is specified.  A `nomatch{}` statement is bound to its closest matching `if` or `else-if` statement.

The if statement has the following form:

```
[label:]
if (condition)
{
  [continue [label]];
  [break [label]];
}
[else if (condition)
{
  [continue [label]];
  [break [label]];
}]
[else
{
  [continue [label]];
  [break [label]];
}]
[nomatch]  // matches else if
{
}
[[nomatch] // matches if
{
}
```

The if statement has the following extensions:

- You can associate a label with the `if` statement in the form `label:`. The label must immediately precede the `if` statement.
- The `break` statement is used to terminate the execution of a navigation path iteration. It can reference an optional label to specify where the Rule resumes executing. The label must be pre-declared preceding the `if` statement.
- The `continue` statement causes early execution of the next iteration of the navigation path. It can reference an optional label to specify where the continuation starts from. The label must be pre-declared preceding the `if` statement.
- The `nomatch` statement is optional.  The code associated with `nomatch` is executed if the navigation path returns 0 rows.  Every `if` construct can be associated with a `nomatch` clause.