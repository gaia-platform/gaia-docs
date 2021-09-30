---
author: Don Glover
owner: 
lastupdate: 09/21/2021
---

---

**NOTE**

The information contained in this document represents information about preview features of the product. Features might change when the product is released for general availability.

---

# Connect and Disconnect

Connect and Disconnect are methods on a field in a table in the Gaia catalog.

## Connect

Connect links rows between two tables based on an existing relationship between the tables.

`[Identifier].field_name1.connect(Table_name2)`

The identifier can be either a table name or a tag.

`Table_name1.connect(Table_name2)`

You can use connect/disconnect directly on tables if there is only one relationship between the two tables. Otherwise, you need to use the link name as defined in the DDL. 

## Disconnect

Disconnect unlinks the rows.

Disconnecting a one to many relationship:

`[identifier].link_name.disconnect(row)`

Disconnecting a one to one relationship:

` [identifier].link_name.disconnect(row)`

## Remarks

Connecting rows allows you to navigate from a row to a connected row using a Navigation Path.
You can use connect/disconnect directly on tables if there is no ambiguity; otherwise, you need to use the link name as defined in the DDL. 

You can specify a tag or a table name for the identifier. For more information about Tags, see Tags.

## Example

Connect and Disconnect for a one to one relationship:

```
on_insert(Flight)
{
  // Connect a new flight to an existing passenger.
  if (/P:Passenger.name == "Bill Clinton")
  {
    // Equivalent to Fight.passenger_1.Connect(P)
    passenger_1.Connect(P);
  }
}
 
on_update(Flight)
{
  // Disconnect a passenger from an existing flight
  if (passenger_1.name == "Bill Clinton")
  {
    // Equivalent to Fight.passenger_1.Disonnect()
    passenger_1.Disconect();
  }
}

on_insert(Passenger)
{
  // Connect a new passenger to an existing flight
  if (/F:Flight.number == 32)
  {
    // Equivalent to Passenger.return_flight_1.Connect(F)
    return_flight_1.Connect(F);
    break;
  }
}
 
on_update(Passenger)
{
  // Disconnect a flight from an existing passenger
  if (return_flight_1.number == 32)
  {
    // Equivalent to Passenger.return_flight.Disconnect()
    return_flight_1.Disconnect();
  }
}

on_insert(Flight)
{
  // Connect multiple passengers to a new flight.
  if (/P:Passenger.name == "Bill Clinton" 
    || P.name == "Greg Fine"
    || P.name == "Wayne Warren")
  {
    // Equivalent to Flight.passengers_M.Connect(P)
    passengers_M.Connect(P);
  }
}
```
 
Connect and Disconnect for a one to many relationship:

```
on_update(Flight)
{
  // Remove multiple passengers from an existing flight.
  if (P:passengers.name == "Bill Clinton" 
    || P.name == "Greg Fine"
    || P.name == "Wayne Warren")
  {
    passengers_M.Disconnect(P);
  }
}
 
on_insert(Passenger)
{
  // Add a flight to a new passenger
  if (/F:Flight.number == 32)
  {
    // Equivalent to Passenger.flight_1.Connect(F)
    flight_1.Connect(F);
    break;
  }
}
 
on_update(Passenger)
{
  // Remove a flight from an existing passenger
  // Equivalent to Passenger->Flight.flight_1.number
  if (flight_1.number == 32)
  {
    // Equivalent to Passenger.flight.Disconnect()
    flight_1.Disconnect();
  }
}
```