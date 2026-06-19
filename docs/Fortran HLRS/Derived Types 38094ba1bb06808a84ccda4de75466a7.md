# Derived Types

---

Derived types are **user-defined data structures** that allow you to group related variables together, similar to:

- `struct` in C
- `class` (without methods) in many OOP languages

They are useful for representing:

- Physical model states
- Model parameters
- Configuration settings
- Collections of related data

---

# **Why Use Derived Types?**

Instead of:

```fortran
real :: psi_x
real :: psi_l
real :: K
real :: C_x
real :: C_l
```

you can write:

```fortran
type PlantState
    real :: psi_x
    real :: psi_l
end type PlantState

type Parameters
    real :: K
    real :: C_x
    real :: C_l
end type Parameters
```

which makes code easier to read and maintain.

---

# **Basic Syntax**

## **Defining a Type**

```fortran
type :: Person
    integer :: age
    character(len=50) :: name
end type Person
```

## **Creating Variables**

```fortran
type(Person) :: person_a
type(Person) :: person_b
```

## Defining Type of Arrays

```fortran
  type :: ForcingSeries
      real, allocatable :: vpd(:)
  end type ForcingSeries
```

---

# **Accessing Components**

Components are accessed using `%`.

```fortran
person_a%age = 35
person_a%name = "Peter"
```

This is equivalent to:

```c
person.age
```

in C or Java.

---

# **Nested Types**

Derived types can contain other derived types.

```fortran
type :: Person
    integer :: age
end type Person

type :: Group
    type(Person) :: employee(50)
    integer :: number_of_employees
end type Group
```

Access nested components with multiple `%`:

```fortran
working_unit%employee(44)%age = 55
```

---

# **Structure Constructors**

Objects can be created using the type name.

```fortran
person_a = Person(age=35, name="Peter")
```

Using keyword arguments is recommended:

```fortran
person_a = Person(age=35, name="Peter")
```

rather than:

```fortran
person_a = Person(35, "Peter")
```

because the order of components can change later.

---

# **Allocatable Components**

Modern Fortran allows dynamic memory inside derived types.

```fortran
type :: Person
    character(len=:), allocatable :: name
end type Person
```

```fortran
type :: Group
    type(Person), allocatable :: employee(:)
end type Group
```

Advantages:

- Variable-length strings
- Dynamically sized arrays
- More flexible data structures

---

# **Derived Types for Parameters**

A common use is storing all model parameters together.

```fortran
type :: Parameters
    real :: K_soil
    real :: K
    real :: C_x
    real :: C_l
    real :: gs_max
end type Parameters
```

Then pass one object:

```fortran
type(Parameters) :: params
```

instead of many separate variables.

Benefits:

- Easier function interfaces
- Easier parameter management
- Easy to add new parameters later

---

# **Derived Types for Model State**

For ODE models it is often useful to store all state variables together.

```fortran
type :: PlantState
    real :: psi_x
    real :: psi_l
end type PlantState
```

Then:

```fortran
type(PlantState) :: state
```

and access values as:

```fortran
state%psi_x
state%psi_l
```

This is exactly the approach you will likely use in your hydraulic model.

---

# **Type Attributes**

## **`private`**

Hide internal details.

```fortran
type :: MyType
    private
    integer :: hidden_value
end type MyType
```

## **`public`**

Allow access from outside the module.

## **`sequence`**

Forces components to appear in memory in declaration order.

Usually **avoid using it** unless interoperability requires it.

---

# **Type-Bound Procedures (Object-Oriented Fortran)**

Types can contain procedures.

```fortran
type :: Table
    real, allocatable :: array(:,:)
contains
    procedure :: read
    procedure :: write
end type Table
```

Used as:

```fortran
call my_table%read()
call my_table%write()
```

This is the basis of object-oriented programming in Fortran.

---

# **Best Practice for Scientific Models**

For most scientific codes:

```fortran
type :: Parameters
    ...
end type Parameters

type :: State
    ...
end type State
```

Store:

- Fixed model parameters in `Parameters`
- Time-evolving variables in `State`

Then pass them around as:

```fortran
call calculate_fluxes(state, params)

state = euler(state, dstate_dt, dt)
```

This keeps the code clean, scalable, and much easier to extend later.