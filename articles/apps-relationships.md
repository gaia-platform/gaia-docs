---
author: Don Glover
owner: 
lastupdate: 10/26/2021
---

# Relationships

In the example DDL, we had a bunch of relationships. The relationships are reflected in the generated classes the same way other columns are.

These are our relationships:

* doctor 1 --> n patient
* patient 1 --> 1 address

Let's look at the generated code:

```cpp
    class address_t : public gaia::direct_access::edc_object_t<...> {
    public:
        ...
        patient_t patient() const;
        ...
    };

    class patient_t : public gaia::direct_access::edc_object_t<...> {
    public:
        ...
        doctor_t doctor() const;
        address_ref_t address() const; 
        ...
    };

    class doctor_t : public gaia::direct_access::edc_object_t<...> {
    public:
        typedef gaia::direct_access::reference_chain_container_t<patient_t> patients_list_t;
        ...
        patients_list_t patients() const;
        ...
    };
    The methods to access the relationships are named after the identifiers used in the DDL references clause:


    table doctor (
        ...
        patients references patient[]
    )

    table patient (
        ...
        doctor references doctor,
        address references address
    )

    table address (
        ...
        patient references patient 
    ) 
```

## One-to-many relationships

Let's consider the doctor 1 --> n patient relationship. The `doctor_t` class exposes the `patients()` method which returns a container of `patient_t` (`patients_list_t)` that allows you to iterate over all of the doctor's patients. Each patient_t has a backlink to the doctor: `doctor_t doctor()`.

`patients_list_t` is a typedef to `reference_chain_container_t<patient_t>`, which is an internal Gaia type compatible with STL containers. The one side of a 1:n relationship has one of these typedefs for every outgoing relationship.

### Connecting objects in 1:n relationships 

To connect objects in a 1:n relationship, you first need to create the objects, and then you can connect them with the `insert()` method:

```cpp
    doctor_t house = doctor_t::get(doctor_t::insert_row("Dr. House"));

    patient_t jane = patient_t::get(patient_t::insert_row("Jane", 183, true));
    patient_t jack = patient_t::get(patient_t::insert_row("Jack", 176, false));
    gaia_id_t john_id = patient_t::insert_row("John", 175, false);

    // Type safe insert.
    house.patients().insert(jane);
    house.patients().insert(jack);

    // Type unsafe insert.
    house.patients().insert(john_id);

    gaia_log::app().info("Num patients: {}", house.patients().size());

```

`insert()` has two overrides:

* One that accepts `gaia_id_t`, which isn't typesafe, and avoids the creation of objects if we already have the Gaia id at hand.
* One that accepts an instance of a Direct Access class, which is typesafe, and does not allow passing instances of the wrong type. For example, you cannot pass an address to `patients().insert()`.

### Traversing objects in 1:n relationships 

Traversing the relationship is straightforward. You can use the enhanced for loop to iterate through the doctor's patients (`house.patients()`) and use the backlink from patients to access the patient's doctor (`patient.doctor()`):

```cpp
    for (auto& patient : house.patients())
    {
        gaia_log::app().info("Patient name: {}", patient.name());
        gaia_log::app().info("Patient's doctor: {}", patient.doctor().name());
    }
```

## Deleting objects in 1:n relationships

When you delete objects involved in relationships, you need to consider referential integrity. In the example above, you cannot delete either side of the relationship before disconnecting the instances; if you do so, you\'ll get an object_still_referenced exception:

```cpp
    house.delete_row();
```

> terminate called after throwing an instance of \'gaia::db::object_still_referenced\' 
>   what(): Cannot delete object with ID \'99\', type \'4211092936\', because it is still referenced by another object with ID \'102\', type \'40335167\'

```cpp
    jane.delete_row();
```

> terminate called after throwing an instance of 'gaia::db::object_still_referenced' 
>   what(): Cannot delete object with ID '102', type '40335167', because it is still referenced by another object with ID \'99\', type \'4211092936 \' To avoid the exception, you need to unlink the objects first:

```cpp
    // You can unlink a single element (there are still 2 connected).
    house.patients().remove(jane);

    // You can now delete the patient.
    jane.delete_row();

    // Unlink all the remaining patients.
    house.patients().clear();

    // You can now delete the doctor.
    house.delete_row();
```

## One-to-one relationships

Let's consider the patient 1 --> 1 address relationship. The `patient_t` class exposes the `address()` method, which returns an object of `address_ref_t`. `address_ref_t` it's a subclass of `address_t`, which allows to treat it as an address, but adds methods for linking/unlinking the address.

The address class has a backlink to `patient_t` via the method `patient()`.

## Connecting objects in 1:1 relationships

To connect objects in a 1:1 relationship, you first need to create the objects, and then you can connect them with the `connect(`) method:

```cpp
    patient_t jane = patient_t::get(patient_t::insert_row("Jane", 183, true));
    address_t kissimmee = address_t::get(address_t::insert_row("Hamlet Ln", "Kissimmee"));
        
    jane.address().connect(kissimmee);

    // You can also pass the id:
    jane.address().connect(kissimmee.gaia_id());
```

## Traversing objects in 1:1 relationships

To traverse the relationship, you can use the `address()` and `patient()` methods:

```cpp
    address_t address = patient.address(); // Actually returns address_ref_t.

    gaia_log::app().info("City {}", address.city());
    gaia_log::app().info("Patient {}", address.patient().name());
```

## Delete objects in 1:1 relationships

Similar to 1:n relationships, you cannot delete objects that are in a 1:1 relationship before unlinking them. Otherwise, you'll get an `object_still_referenced` exception:

```cpp
    patient.delete_row();
```

> terminate called after throwing an instance of 'gaia::db::object_still_referenced' 
>  what(): Cannot delete object with ID '81', type '40335167', because it is still referenced by another object with ID '82', type '1084090863'

```cpp
    address.delete_row();
```

> terminate called after throwing an instance of 'gaia::db::object_still_referenced' 
>   what(): Cannot delete object with ID '82', type '1084090863', because it is still referenced by another object with ID '81', type '40335167'

To avoid the exception:

```cpp
    patient.address().disconnect();

    patient.delete_row();
    address.delete_row();
```