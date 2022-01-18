---
author: Don Glover
owner: 
lastupdate: 01/11/2022
---

---

NOTE

The information contained in this document represents information about preview features of the product. Features might change when the product is released for general availability.

---

# Navigation Paths

In Gaia, references to a field in the database can take two forms:

* A Reference directly to a single row and field (for example Flight.FlightNumber),
* A Navigation Path that refers to a set of related data (For example Flight.Passengers->FirstName) through table relationships and can represent 0, 1, or more results.

You can declare a Navigation Path explicitly as a path through data relationships expressed as references. While this allows you to identify each relationship and to know exactly how the Path is constructed, it can result in long, unwieldy names that reduce comprehension.

Alternatively, you can choose to use abbreviated Navigation Paths. When Gaia can derive the references from the data, you can omit the explicit segments in the path to create an abbreviated Navigation Path. This can result in clearer and simpler Rule code.

A Rule statement can contain multiple Navigation Paths. Each Navigation Path in the statement can expand to 0 or more results. This means that the number of times the statement executes can be the product of the result counts of each Navigation Path. For example, if a statement contains three Navigation Paths, with result set sizes 3, 1, and 15, the statement might execute up to 3x1x15 times.

The following Ruleset example shows two rules that contain a multi "segment" path. The first rule iterates using explicit navigation while the second rule iterates using in implicit navigation. 

Each Rule fires when the clinic is closed. 

```c++
ruleset clinics_ruleset
{
    on_update(clinic.closed)
    {
        if(closed==true)
        {
            for(C:clinic->D:doctor->P:patient)
            {
                gaia_log::app().info(
                    "{} patient of doctor {} in clinic {} notified of closure using explicit navigation.", P.name, D.name, C.name);
            }
        }
        else
        {
            gaia_log::app().info(
                "{} is open. All patients welcome.", clinic.name);
        } 
    }

    on_update(clinic.closed)
    {
        if(clinic.closed==true)
        {
            gaia_log::app().info(
                "{} patient of doctor {} in clinic {} notified of closure using implicit navigation.", C:clinic->D:doctor->patient.name, D.name, C.name);
        }
    }
}
```

In the following sample run, there are two clinics. Each clinic as two doctors, each of which has two patients. When a Rule fires, it iterates four times; once for each patient.


```
[2022-01-18T12:54:04.289] [info] [12886 12886] <app>: Setting Mal's Practice to closed.
[2022-01-18T12:54:04.290] [info] [12886 12886] <app>: Setting Doc in the Box to closed.
[2022-01-18T12:54:04.290] [info] [12886 12913] <app>: Leela patient of doctor Who in clinic Mal's Practice notified of closure using explicit navigation.
[2022-01-18T12:54:04.290] [info] [12886 12908] <app>: Leela patient of doctor Who in clinic Mal's Practice notified of closure using implicit navigation.
[2022-01-18T12:54:04.290] [info] [12886 12913] <app>: Rose patient of doctor Who in clinic Mal's Practice notified of closure using explicit navigation.
[2022-01-18T12:54:04.290] [info] [12886 12908] <app>: Rose patient of doctor Who in clinic Mal's Practice notified of closure using implicit navigation.
[2022-01-18T12:54:04.290] [info] [12886 12913] <app>: Clea patient of doctor Strange in clinic Mal's Practice notified of closure using explicit navigation.
[2022-01-18T12:54:04.290] [info] [12886 12908] <app>: Clea patient of doctor Strange in clinic Mal's Practice notified of closure using implicit navigation.
[2022-01-18T12:54:04.290] [info] [12886 12913] <app>: Wong patient of doctor Strange in clinic Mal's Practice notified of closure using explicit navigation.
[2022-01-18T12:54:04.290] [info] [12886 12908] <app>: Wong patient of doctor Strange in clinic Mal's Practice notified of closure using implicit navigation.
[2022-01-18T12:54:04.291] [info] [12886 12886] <app>: Setting Mal's Practice to open.
[2022-01-18T12:54:04.291] [info] [12886 12912] <app>: Maurice patient of doctor Bombay in clinic Doc in the Box notified of closure using explicit navigation.
[2022-01-18T12:54:04.291] [info] [12886 12909] <app>: Maurice patient of doctor Bombay in clinic Doc in the Box notified of closure using implicit navigation.
[2022-01-18T12:54:04.291] [info] [12886 12912] <app>: Samantha patient of doctor Bombay in clinic Doc in the Box notified of closure using explicit navigation.
[2022-01-18T12:54:04.291] [info] [12886 12909] <app>: Samantha patient of doctor Bombay in clinic Doc in the Box notified of closure using implicit navigation.
[2022-01-18T12:54:04.291] [info] [12886 12912] <app>: Mayfair patient of doctor Savage in clinic Doc in the Box notified of closure using explicit navigation.
[2022-01-18T12:54:04.291] [info] [12886 12909] <app>: Mayfair patient of doctor Savage in clinic Doc in the Box notified of closure using implicit navigation.
[2022-01-18T12:54:04.291] [info] [12886 12912] <app>: Littlejohn patient of doctor Savage in clinic Doc in the Box notified of closure using explicit navigation.
[2022-01-18T12:54:04.291] [info] [12886 12909] <app>: Littlejohn patient of doctor Savage in clinic Doc in the Box notified of closure using implicit navigation.
[2022-01-18T12:54:04.291] [info] [12886 12886] <app>: Setting Doc in the Box to open.
[2022-01-18T12:54:04.291] [info] [12886 12911] <app>: Mal's Practice is open. All patients welcome.
[2022-01-18T12:54:04.292] [info] [12886 12919] <app>: Doc in the Box is open. All patients welcome.
```

Navigation Paths occur when a Rule contains a reference to a field in the database.

For example:

`Traveler.MilesFlown`

This essentially says:

Select the MilesFlown from Traveler for the current anchor row.

Navigate: flight->segment->trip->traveller

Iterate: flight->segment is 1:m -- update all passengers on flight

A Navigation Path iterates over the set of rows related to the anchor. To iterate over all of the rows in a table, independent of any relationships, use the "/" operator.

flight->Segment->Trip->Traveler

You can use multiple navigation paths in a single statement. This creates multiple iterations of the returned sets of rows which are evaluated from left to right.

## Abbreviated Navigation Paths

An abbreviated Navigation Path is one in which a database field or table reference does not start with an absolute path (that is, the anchor row, a defined tag, or an "entire table" notation - "/").

Gaia analyzes the DDL and locates the shortest path between the anchor and the field or table. The anchor row is invariant through the entire Rule  and is the starting point for every abbreviated Navigation Path.

A Navigation path can contain abbreviated components. The following example shows abbreviated components in a complete Navigation Path.

<example>

```c++
on_update(Traveller)
{
   print(FlightMiles);
   // FlightMiles is found via Traveller. Various paths are equivalent:
   // Traveller->Trip->Segment->Flight.FlightMiles
   // Trip->Segment->Flight.FlightMiles
   // Segment->Flight.FlightMiles
   // Flight.FlightMiles
   // FlightMiles
}
```

Why not always abbreviate? Some common reasons are:

* There are multiple paths from the anchor to the field, and you want to make sure the correct one is used.
* There are two or more paths that are of equal length between the anchor and field, and you are forced to pick one.
* You want to tag a segment of the path in order to refer to its fields. For example:

```c++
on_update(Traveller)>
{
   for (T:Trip->Segment->F:Flight)
   {
      print(T.TripMiles, F:FlightMiles);
   }
}
```

Navigation Paths with multiple relationships (need better heading)

The Table->Table[.fieldname] navigation path syntax is applicable when only a single relationship exists between the related tables. However, suppose a table is related to another table via multiple relationships. In that case, you need to use the relationship name in the navigation path instead of the table name to disambiguate which path you wanted to follow.

The following DDL is a fragment from a sample course registration system. It defines two tables, course which contains the available courses and prerequisites which contains prerequisites for courses. It also defines two relationships:

* `course_prereq` which is a many-to-many relationship between courses where one direction is all of the courses required as prerequisites to a course.
* `prereq_course` which is all the courses for which this is the prerequisite of.

```sql
create table if not exists course (
   course_id string,
   name string,
   hours int32
);

create table if not exists prereq (
   prereq_id string,
   min_grade string
);

create relationship if not exists prereq_course (
   course.required_by -> prereq[],
   prereq.prereq -> course
);

create relationship if not exists course_prereq (
   course.requires -> prereq[],
   prereq.course -> course
);
```

The following is a snippet from a Ruleset the defines navigation paths based on the DDL.

```c++
// Path for list of courses that are required by this course.
course.required_by->prereq.course->course
// Path for list of courses that require this course
course.requires->prereq.prereq->course
```