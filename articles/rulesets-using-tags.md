---
author: Don Glover
owner: Don Glover
lastupdate: 05/28/2021
---

---

**NOTE**

The information contained in this document represents information about preview features of the product. Features might change when the product is released for general availability.

---

# Using Tags

When performing iterations in a Navigation Path, it is convenient to have a way to reference the current row. To address this, Gaia provides a tag feature that allows you to automatically create a local variable that you can use to work with the row in question. 

A Tag is a special form of identifier. It has a type, which is the table type it is attached to. Its value is a row of that type (table). A Tag can be any valid C++ variable name. Tags must be unique within the scope of the Rule.  Declaring a local variable that has the same name as a Tag causes an error at the time of translation.

Tags are explicitly associated with a segment of a navigational path and are used to represent the current row of the segment type during an iteration of the path. The scope of the tag is limited to the statement in which it is defined.
Tags are defined in the form:

tag:table-name

For example:
Flight->Segment->Trip->T:Traveller

Tags represent "the current row of a table,” allowing the Rule to access fields and relationships in that row by using the tag within the scope of the statement that encloses the tag definition.

The Tag creates a local variable that points to travelers one by one as we iterate through the people on a flight. So, T.MemberMiles references the miles for the passenger with which we are currently working. Tags become particularly important as we consider dealing with multiple lists in the next section.

Any single statement also defines the scope of a tag. Within the scope of its usage, the tag becomes the name of an instance of a single row in the table. 

```
        // note:  If you want to reference the current row in a for statement
        // then you must use a tag.
        for (/S:student)
        {
            if (strcmp(S.student_id, "stu001") == 0)
            {
                S.registrations.connect(registration);
                break;
            }
        }
```

The scope for an on_xxxx() statement is the entire Rule. This can be a single-line statement, or a more complex statement like for, while, or if.

You can define multiple tags in a single statement.

## Using tags in an on_xxxx() prefix

Tags can appear in the on_xxxx() prefix to a Rule or in a variable reference. The Tag then represents the Rule's anchor row throughout the body of the Rule. However, the name must not conflict with any other name used in the Rule or on_xxxx statement (table, field, or other variables.)

## In a Navigation Path

You can use any previously set  tag in a navigation path as the source row for a subsequent in-scope navigation path declaration. The generalized form, shown below, allows tag0 to be any tag that is valid and visible in the current lexical scope:

tag0->Segment1->Segment2->...->Segmentn

In the above notation, the first tag is being referenced or "used", while all others that appear in the path (as an optional part of the Segment) are being set or defined.

Flight->Segment->Trip->T:Traveller
Traveller.Name
T.Name
