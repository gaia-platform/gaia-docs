---
author: Don Glover
owner: 
lastupdate: 09/21/2021
---

---

**NOTE**

The information contained in this document represents information about preview features of the product. Features might change when the product is released for general availability.

---

# serial_group

Specifies that all of the Rules within the Ruleset are guaranteed to be serialized. The optional group_name parameter allows you to use the serial_group attribute in multiple Rulesets to ensure that all of the Rules in the Rulesets that specify the same group_name are serialized as a group.

The serial_group attribute has the following form:

`ruleset ruleset_name : serial_group(group_name)`

group_name: An optional identifier that Gaia uses to group rulesets for serializing.

Remarks

Gaia's default execution model is to run Rules in parallel. Your application could contain Rules that need to be run sequentially. For example, your app has a single field that must have a guaranteed to be correct value. To ensure that this value is only modified by one instance of a rule at time, use the serial_group attribute.

To specify that all of the Rules within a Ruleset are serialized, use the serial_group attribute as part of the Ruleset header. If you have multiple Rulesets that must be serialized, use the optional group_name parameter to ensure that all of the Rules from all Rulesets with the matching group_name are treated as a single set of serialized rules.

**Note**: When specifying the serial_group attribute, the rules are run strictly in the order in which they fire. This includes separate instances of the same rule.

Example

```c++
ruleset BuildingPopulationTracker : serial_group(Pop_Tracker)
{
    on_update(scan badge_scan)
    {
        building.visitors++;
        building.population++;
    }
    on_update(scan badge_exit_scan)
    {
        building.population--;
    }
}
```

Where the scan has a reference to the building that it is in and the building has a field to track the number of people entering and leaving via entry and exit badge scans. We’re also tracking the number of visitors to date, because why not? Feel free to change this up, but this is the essential requirement - we have a single field that we need to have a guaranteed-to-be-correct (fire insurance requirement?) value for.
