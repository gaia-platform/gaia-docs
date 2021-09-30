---
author: Don Glover
owner: 
lastupdate: 09/21/2021
---

---

**NOTE**

The information contained in this document represents information about preview features of the product. Features might change when the product is released for general availability.

---

# rule_context structure

Contains rule specific information that you can use to log which rule fired. 

```
rule_context.rule_name 
rule_context.ruleset_name 
rule_context.event_type 
rule_context.gaia_type 
```

*rule_name*<br>
The ordinal corresponding to the Rule name.

*ruleset_name*<br>
A string containing the name of the Ruleset.

*event_type*<br>
An event_type_t enumeration value

*gaia_type* <br>
The numeric type ID of the rule.

## Remarks

You can view the Gaia type by running [gaiac](../tools/tool-gaiac.md) in interactive mode and using the list "\l" commands.
