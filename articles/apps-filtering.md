---
author: Don Glover
owner: 
lastupdate: 10/26/2021
---

# Filtering

Direct Access provides a filtering API. The core of the filtering API is the `where()` method exposed by each Gaia container which takes a predicate as input and returns a container with the filtered elements as output.

The predicate is expressed as `std::function<bool (const T_class&)where T_class` is the Direct Access class contained by the container.

## Lamda predicates

You can create arbitrary predicates through Lambda:

```cpp
    auto doctors = doctor_t::list().where(
        [](const doctor_t& p)
        { return strcmp(p.name(), "Dr. House") == 0; });

    if (doctors.begin() == doctors.end())
    {
        gaia_log::app().warn("No doctors found.");
        return;
    }

    // Assuming there is only one result.
    doctor_t house = *doctors.begin();
```

Relationship containers also expose the `where()` method. In this example, we are searching among the patients of a specific doctor:

```cpp
    auto patients = house.patients().where(
        [](const patient_t& p)
        { return strcmp(p.name(), "Jack") == 0; });

    if (patients.begin() == patients.end())
    {
        gaia_log::app().warn("No patients found!");
        return;
    }

    // Assuming there is only one result.
    patient_t jack = *patients.begin();
```

## Gaia Predicates

As part of the Direct Access classes, we generate predicates that are more expressive than raw C++ lambdas.

If you look at the `doctor_t` class, you will see an expr_ section and right outside the class a `doctor_exp    r` namespace. That namespace contains the predicates that you can use instead of the lambdas.

```cpp
    class doctor_t : public gaia::direct_access::edc_object_t<...> {
    ...
        struct expr_ {
            static gaia::direct_access::expression_t<doctor_t, gaia::common::gaia_id_t> gaia_id;
            static gaia::direct_access::expression_t<doctor_t, const char*> name;
            static gaia::direct_access::expression_t<doctor_t, doctor_t::patients_list_t> patients;
        };
        using expr = expr_<void>;
    ...
    };

    namespace doctor_expr {
        static auto& gaia_id = doctor_t::expr::gaia_id;
        static auto& name = doctor_t::expr::name;
        static auto& patients = doctor_t::expr::patients;
    };
```

## Operators on strings

String fields support == and != operators, allowing you to rewrite the example above as:

```cpp
    auto doctors = doctor_t::list().where(doctor_expr::name == "Dr. House");

    if (doctors.begin() == doctors.end())
    {
        gaia_log::app().warn("No doctors found!");
        return;
    }

    // Assuming there is only one result.
    doctor_t house = *doctors.begin();

    auto patients = house.patients().where(patient_expr::name == "Jack");

    if (patients.begin() == patients.end())
    {
        gaia_log::app().warn("No patients found!");
        return;
    }

    patient_t jack = *patients.begin();
```

## Operators on numbers

Numerical fields support all the basic operators on numerical values: >, >=, <, <=, ==, !=.

```cpp
    auto female_patients = patient_t::list().where(patient_expr::is_female == true);
    gaia_log::app().info("There are {} female patients", female_patients.size());

    auto higher_than_160 = patient_t::list().where(patient_expr::height >= 160);
    gaia_log::app().info("There are {} patients higher than 160", higher_than_160.size());
```

## Operators on containers

It is possible to evaluate predicates on relationships. The supported predicates are: `contains`, `empty`, `count`.

`contains()` evaluates to **true** when a container contains a certain predicate or a specific object. The following example looks for all the doctors that have a specific patient:

```cpp
    // Contains with predicate.
    auto jacks_doctor_container = doctor_t::list().where(
        doctor_expr::patients.contains(
            patient_expr::name == "Jack"));

    auto jacks_doctor = *jacks_doctor_container.begin();
    gaia_log::app().info("Jack's doctor is {}", jacks_doctor.name());
            
    // Contains with specific object.
    auto jane = patient_t::get(...);

    auto jane_doctor_container = doctor_t::list().where(
        doctor_expr::patients.contains(jane));

    auto janes_doctor = *jane_doctor_container.begin();
    gaia_log::app().info("Jane's doctor is {}", janes_doctor.name());
```

`empty()` evaluates to **true** if a container is empty. the following example searches for all the doctors with no patients:

```cpp
    doctor_t::list().where(doctor_expr::patients.empty());
```

`count()` evaluates to **true** when a container contains the specified number of elements:

```cpp
    auto doctors_with_one_patient = doctor_t::list().where(
        doctor_expr::patients.count() >= 1);

    for (doctor_t& doctor : doctors_with_one_patient)
    {
        gaia_log::app().info("{} has at least one patient", doctor.name());
    } 
```