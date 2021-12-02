---
author: 
owner: 
lastupdate: 
---

---

**NOTE**

The information contained in this document represents information about preview features of the product. Features might change when the product is released for general availability.


---

# Container Class API

The following are exposed as part of the container `patient _list_t`.

Examples in these articles are taken from the Direct Access sample app that is installed with the SDK at /opt/gaia/examples/direct_access.

`patients_t::list()`

## Methods on the container

**insert**

`patients_t::insert()` - Creates a relationship between the rows.

`insert()` has two overrides:

* One that accepts a gaia_id_t, which isn't typesafe, and avoids the  creation of objects if you already have the Gaia id.
* One that accepts an instance of a Direct Access class, which is typesafe, and does not allow passing instances of the wrong type.

Synonym for disconnect.

**remove**

`patients_t::<list name>().remove()`

Breaks a relationship between rows.

Synonym for disconnect.

**connect**

`patients_t::<list name>().connect()`

Aliased to insert.

**disconnect**

`patients_t::<list name>().disconnect()`

Aliased to remove.

**clear**

`patients_t::<list name().clear()`

Removes all relationships (1 side of 1:1 or N side of 1:N) from this reference.

**erase**

`patients_t::list name().erase()`

Removes a Value Linked Reference from the list.

The `erase()` method is functionally the equivalent to the `disconnect()` method except that it takes an iterator that points to the item to disconnect and returns an iterator to the next item in the list.

**size**

`patients_t::list name().size()`

Gets the number of linked rows.

Examples:

```cpp
patient_t jane = patient_t::get(patient_t::insert_row(Jane, 183, true));
address_t kissimmee = address_t::get(address_t::insert_row(Hamlet Ln, Kissimmee));
jane.address().connect(kissimmee);

// You can also pass the id:
jane.address().connect(kissimmee.gaia_id());
void delete_one_to_many_relationship(gaia_id_t doctor_id)
{
    PRINT_METHOD_NAME();
    doctor_t doctor = doctor_t::get(doctor_id);
    
    // Pick one of the doctor\'s patients.
    patient_t patient = *(doctor.patients().begin());
    
    // You can unlink a single element (there are still 2 connected).
    doctor.patients().remove(patient);
    
    // You can now delete the patient.
    patient.delete_row();
    
    // Unlink all the remaining patients.
    doctor.patients().clear();
    
    // You can now delete the doctor.
    doctor.delete_row();
}
```

## Methods on Value Linked Relationships

`patients_t::list().begin()`

`patients_t::list().end()`

`patients_t::list().where()`

The `where()` takes a predicate as input and returns a container with the filtered elements as output.
The predicate is expressed as `std::function bool (const > T_class&)` where the T_class is the Direct Access class contained by the container.

`edc_invalid_object_type`: Argument does not match the class type.

Example

```cpp
// Contains with expression.
auto jacks_doctor_container = doctor_t::list().where(
doctor_expr::patients.contains(
patient_expr::name == Jack));
auto jacks_doctor = *jacks_doctor_container.begin();
gaia_log::app().info("Jack's doctor is {}", jacks_doctor.name());

// Contains with constant.
auto jane = *(patient_t::list().where(patient_expr::name ==Jane).begin());
auto janes_doctor_container = doctor_t::list().where(
    doctor_expr::patients.contains(jane));
    auto janes_doctor = *janes_doctor_container.begin();
    gaia_log::app().info("Jane's doctor is {}", janes_doctor.name());
    auto doctors_with_no_patients =
        doctor_t::list().where(doctor_expr::patients.empty());

    if (doctors_with_no_patients.begin() == doctors_with_no_patients.end())
    {
        gaia_log::app().info(All the doctors have at least one patient);
    }
...
```

## **Operators on containers**

The following methods are used to evaluate predicates on relationships.  

**contains**

`contains()`

Evaluates to true when at least one object in the container matches a filter predicate.

**empty**

`empty()`

Evaluates to true if a container is empty.

**count**

`count()`

Evaluates to true when a container contains the specified number of elements.

## Example

The following code snippet shows the usage of the `contains()` and `empty()` methods.

```cpp
// Contains with expression.
auto jacks_doctor_container = doctor_t::list().where(
    doctor_expr::patients.contains(patient_expr::name == Jack));
auto jacks_doctor = *jacks_doctor_container.begin();
gaia_log::app().info("Jack\'s doctor is {}", jacks_doctor.name());

// Contains with constant.
auto jane = *(patient_t::list().where(patient_expr::name == Jane).begin());
auto janes_doctor_container = doctor_t::list().where(
    doctor_expr::patients.contains(jane));
auto janes_doctor = \janes_doctor_container.begin();
gaia_log::app().info("Jane's doctor is {}", janes_doctor.name());
auto doctors_with_no_patients =
    doctor_t::list().where(doctor_expr::patients.empty());
...
```