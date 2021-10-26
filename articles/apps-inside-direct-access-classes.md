---
author: Don Glover
owner: 
lastupdate: 10/26/2021
---

# Inside the Direct Access Classes

What's inside the generated code, and how do you use it?

If you look inside gaia_hospital.h you will find one c++ class for each table in the specified database (-d hospital) when gaiac was run. Classes are not generated for tables outside of the requested database; you need to rerun gaiac with a different database to generate those classes.

The Direct Access Classes map the structure of the DB table at the time of generation. Let\'s take a look at a simplified version of the `patient_t` class:

```cpp
    typedef gaia::direct_access::edc_writer_t<...> patient_writer;
    class patient_t : public gaia::direct_access::edc_object_t<...> {
    public:
        patient_t() : edc_object_t() {}
        
        // Static helpers.
        static const char* gaia_typename();
        static gaia::common::gaia_id_t insert_row(const char* name, uint8_t height, bool is_female);
        static gaia::direct_access::edc_container_t<c_gaia_type_patient, patient_t> list();
        
        // Primitive type fields.
        const char* name() const;
        uint8_t height() const;
        bool is_female() const;
        
        // Relationships.
        doctor_t doctor() const;
        address_ref_t address() const; 
        ...
    private:
        // You can build objects by calling patient_t::get(gaia::common::gaia_id_t).
        explicit patient_t(gaia::common::gaia_id_t id) : edc_object_t(id) {}
    };
```
 
The most important aspect of this class is the presence of one method per table column (`name()`, `height()`, `is_female()`). You can use these methods to access the column value of a given record. Each DDL type maps to a CPP type.

There are methods for relationships too. We will look at those later in the document.

## Create DB records

There are two ways of creating DB records:

* The `insert_row()` method.
* The `_writer` object.

`insert_row()` accepts a list of values corresponding to the primitive fields. The `patient` table has three primitive columns `name`, `height`, and `is_female`; hence you need to specify a value for them all as parameters:

```cpp
    gaia_id_t id = patient_t::insert_row("Jane", 183, true);
    // You can later use the id to lookup the record.
```

The `_writer` object allows you to specify only some of the values. Values that are not specified are assigned a default value. In this example, we do not specify a
height; hence the value will default to 0.

```cpp
    patient_writer patient_w ;
    patient_w.name = "Jane";
    patient_w.is_female = true;
    gaia_id_t id = patient_w.insert_row();
```

For more information on default values, see [insert and remove](reference/declarative-insert-remove.md).

## Retrieve specific DB records

In the examples above, we obtained a `gaia_id_t` as a result of the record creation. You can use the id to retrieve the record:

```cpp
    patient_t jane = patient_t::get(jane_id);
```

**Note**: the get method is not directly exposed by the `patient_t class` but is inherited from its superclass.

Direct Access also provides a filtering API; we'll look into it in the following sections.

Each EDC class overrides the `bool()` operator so that you can evaluate its validity with if:

```cpp
    gaia_id_t invalid_id = 12344;
    patient_t john = patient_t::get(invalid_id);

    if (john)
    {
        gaia_log::app().info("Patient name: {}", john.name());
    }
    else
    {
        gaia_log::app().info("Cannot find patient with id: {}", invalid_id);
    }
```

If you access an invalid record, you get an `invalid_object_id` exception:

```cpp
    patient_t john = patient_t::get(12344);
    john.name();
```

> terminate called after throwing an instance of 'gaia::db::invalid_object_id\' 
>   what(): Cannot find an object with ID '0'.


## Iterate through all table records

The `list()` method exposes an iterator over all the records in a table:

```cpp
    for (auto& patient : patient_t::list())
    {
        gaia_log::app().info(
            "Patient name:{}, height:{}", patient.name(), patient.height());
    }
```

## Delete DB records

To delete a record, use the `delete_row()` method. This method is inherited from the `doctor_t` superclass.

```cpp
    doctor_t house = doctor_t::get(doctor_t::insert_row("Dr. House"));
    house.delete_row();

    if (!house)
    {
        gaia_log::app().info("The record has been deleted");
    }
```

If you want to remove all the records in a table, you may be tempted to do the following:

```cpp
    doctor_t house = doctor_t::get(doctor_t::insert_row("Dr. House"));
    doctor_t dorian = doctor_t::get(doctor_t::insert_row("Dr. Dorian"));
    doctor_t reid = doctor_t::get(doctor_t::insert_row("Dr. Reid"));

    for (auto& doctor : doctor_t::list())
    {
        doctor.delete_row();
    }
```


> terminate called after throwing an instance of'gaia::db::invalid_object_id\'
>  what(): Cannot find an object with ID '0'.


This approach does not work because you are modifying the container while iterating over it. 

One of the correct ways to do this is:

```cpp
    for (auto& doctor = *doctor_t::list().begin();
         doctor; doctor = *doctor_t::list().begin())
    {
        doctor.delete_row();
    }

    gaia_log::app().info("Num doctors: {}", doctor_t::list().size());
```

> Num doctors: 0


In the following sections, we will deal with the referential integrity in cases where the doctor is connected to other tables.
