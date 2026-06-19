# Overview of All Structures

At a high level, modern Fortran programs are built from a small number of program units:

```
Main Program
│
├── Modules
│   ├── Subroutines
│   ├── Functions
│   ├── Derived Types
│   └── Interfaces
│
├── Submodules
│
├── External Procedures
│   ├── Subroutines
│   └── Functions
│
└── Block Data (legacy)
```

---

# **Program**

The executable entry point.

```fortran
program main
    implicit none
    ...
end program main
```

Usually there is only one.

Calls the subroutines and functions etc.

---

# **Module**

A container for code and data.

```fortran
module math_utils
    implicit none

contains

    subroutine hello()
        print *, "Hello"
    end subroutine

end module math_utils
```

Imported with:

```fortran
use math_utils
```

Modules are the backbone of modern Fortran.

---

# **Subroutine**

A procedure that performs actions.

```fortran
subroutine greet(name)
    character(*) :: name
    print *, "Hello ", name
end subroutine
```

Called with:

```fortran
call greet("Brian")
```

Similar to a Python function that returns nothing.

---

# **Function**

A procedure that returns a value.

```fortran
function square(x) result(y)
    real :: x, y
    y = x*x
end function
```

Used as:

```fortran
a = square(5.0)
```

---

# **Derived Type**

Fortran’s equivalent of a struct/class.

```fortran
type :: tree
    real :: height
    real :: diameter
end type tree
```

Usage:

```fortran
type(tree) :: oak

oak%height = 12.0
```

The `%` operator accesses components.

---

# **Interface**

Describes procedures.

```fortran
interface
    subroutine mysub(x)
        real :: x
    end subroutine
end interface
```

Often generated automatically by modules nowadays.

---

# **Submodule**

Used to separate implementation from interface.

Module:

```fortran
module maths
contains
    module subroutine solve()
    end subroutine
end module
```

Submodule:

```fortran
submodule(maths) maths_impl
contains
    module procedure solve
        ...
    end procedure
end submodule
```

Large codes sometimes use these to reduce compile times.

You probably won’t need them for a while.

---

# **Block**

Temporary local scope.

```fortran
block
    integer :: i
    ...
end block
```

Useful for local variables which are only retained inside the block

---

# **Program Structure in Large Models**

A typical Earth-system model looks roughly like:

```
Program
│
├── Module: Constants
├── Module: Parameters
├── Module: Soil
├── Module: Vegetation
├── Module: Hydraulics
├── Module: Photosynthesis
├── Module: Output
│
└── Main Driver
       │
       ├── call soil_step()
       ├── call hydraulics_step()
       ├── call photosynthesis_step()
       └── call output_step()
```

So if you’re prioritizing what to learn next, I’d suggest:

```
1. Programs
2. Modules
3. Subroutines
4. Functions
5. Derived Types
6. Interfaces
7. Submodules
```

Modules, subroutines, functions, and derived types make up probably **95% of the structure you’ll encounter in modern scientific Fortran code**.