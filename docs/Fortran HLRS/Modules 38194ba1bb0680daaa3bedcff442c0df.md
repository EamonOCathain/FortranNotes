# Modules

# **Modules**

A **module** is a program unit used to organize and share data, procedures, and derived types.

Modules help structure large programs by grouping related code into a single reusable unit.

Modern Fortran programs should be organized around modules.

It is an encapsulation mechanism.. it can encapsulate:

- shared (global) data
- derived types
- procedures
- interfaces

---

# **Why Use Modules?**

As programs grow larger, it becomes difficult to manage all variables and procedures in one file.

Modules allow related functionality to be grouped together.

For example:

- Mathematical functions
- File I/O routines
- Physical constants
- Model parameters
- Derived types

can each be placed in their own module.

This improves:

- readability
- maintainability
- code reuse
- debugging

---

# **Modules vs Procedures vs Module Procedures**

A module is a **container** that organizes and shares code.

The module itself does not perform calculations.

It simply contains and organizes things.

The procedures are the **things inside the container**.

A procedure performs work.

Procedures are:

- Functions
- Subroutines

A **module procedure** is simply a function or subroutine that lives inside a module.

### Module Procedure Example

```fortran
module maths

contains

    function square(x) result(y)

        real, intent(in) :: x
        real :: y

        y = x**2

    end function square

end module maths
```

---

# **Basic Structure**

A module has two main parts separated by the keyword:

```fortran
contains
```

General structure:

```fortran
module module_name

    implicit none

    ! Specification part

contains

    ! Procedures

end module module_name
```

The specification part contains declarations.

The procedure part contains functions and subroutines.

---

# **Specification Part**

The specification part appears before `contains`.

It may contain:

- variables
- constants
- derived types
- interfaces
- procedure declarations

Example:

```fortran
module constants

    implicit none

    real, parameter :: pi = 3.14159

end module constants
```

---

# **Intrinsic Modules**

See the independent page on iso_fortran_env [here](Iso_fortran_env%20Module%2038294ba1bb068014aea9cdd4fd178731.md)

Some modules are provided automatically by the compiler.

The most commonly used is:

```fortran
use iso_fortran_env
```

This module provides portable definitions such as:

```fortran
int8
int16
int32
int64

real32
real64
real128
```

Using these definitions helps make programs portable between different systems and compilers.

---

# **Procedure Part**

The procedure part appears after `contains`.

It contains the implementation of functions and subroutines.

Example:

```fortran
module maths

    implicit none

contains

    function square(x) result(y)

        real, intent(in) :: x
        real :: y

        y = x**2

    end function square

end module maths
```

Functions and subroutines defined inside a module are called **module procedures**.

---

# **Internal Procedures**

A procedure may contain other procedures using `contains`.

```fortran
subroutine outer()

    integer :: x

contains

    subroutine inner()

        print *, x

    end subroutine inner

end subroutine outer
```

The nested procedure is called an **internal procedure**.

Internal procedures:

- can only be called by their host procedure
- automatically have access to variables declared in the host procedure
- are useful for helper routines that are not needed elsewhere

---

# **External Procedures**

A procedure defined outside a module is called an **external procedure**.

```fortran
function square(x)

    real :: square
    real :: x

    square = x**2

end function square
```

External procedures are common in older Fortran programs and libraries.

Modern Fortran generally prefers module procedures because the compiler can perform better checking of arguments and interfaces.

---

# **Using a Module**

A program accesses the contents of a module using the `use` statement.

```fortran
use module_name
```

This makes the module’s public variables, derived types, functions, and subroutines available to the program.

Example:

```fortran
program example

    use maths

    implicit none

    print *, square(5.0)

end program example
```

Output:

```
25.0
```

---

# **Importing Specific Names**

By default, all public entities from a module are imported.

To import only selected names, use:

```fortran
use module_name, only : name1, name2
```

Example:

```fortran
use constants, only : pi
```

Using `only` makes dependencies clearer and helps avoid name conflicts.

---

# **Renaming Imports**

Imported names can be given a different local name using:

```fortran
use module_name, local_name => name_in_module
```

Example:

```fortran
use constants, circle_pi => pi
```

The variable `pi` from the module is now available locally as `circle_pi`.

Renaming can be useful when:

- two modules contain variables with the same name
- a more descriptive local name is desired
- name conflicts need to be avoided

---

# **Sharing Variables**

Variables declared in a module can be accessed by any program or procedure that uses the module.

Example:

```fortran
module constants

    implicit none

    real, parameter :: pi = 3.14159

end module constants
```

Used as:

```fortran
program circle

    use constants

    implicit none

    print *, pi

end program circle
```

Module variables provide a convenient way to share information throughout a program.

However, frequently modified module variables behave like global variables and should be used carefully, as they can introduce side effects and make programs more difficult to understand and maintain.

---

# **Global Variables and Side Effects**

Variables declared in a module are visible to any program unit that uses the module.

```fortran
module settings

    integer :: nsteps

end module settings
```

Although convenient, excessive use of global variables is discouraged.

Writing to global variables creates side effects and can make programs harder to understand, test, and parallelise.

Constants are usually a better use case for module variables than frequently modified state.

### Module Variables Become Global Variables

For example:

```fortran
module settings

    integer :: nsteps

end module settings
```

Any procedure that does:

```fortran
use settings
```

can modify `nsteps`.

---

# **Derived Types in Modules**

Modules are commonly used to store derived types.

Example:

```fortran
module geometry

    implicit none

    type :: Point

        real :: x
        real :: y

    end type Point

end module geometry
```

This allows the type to be reused throughout a program.

---

# **Module Dependencies**

Modules may use other modules.

```fortran
module physics

    use constants

end module physics
```

This allows related functionality to be built in layers.

A program may therefore depend on several modules, and modules may depend on one another.

For example:

```
constants_module
        ↓
physics_module
        ↓
main program
```

---

# **Modules Have No Arguments**

Unlike functions and subroutines, modules do not have argument lists.

Valid:

```fortran
module maths
```

Invalid:

```fortran
module maths(x)
```

Modules provide definitions rather than perform calculations.

---

# **Modules Cannot Contain a Main Program**

A module may contain:

- variables
- constants
- derived types
- functions
- subroutines

A module cannot contain a main program.

Valid:

```fortran
module maths

contains

    function square(x) result(y)

        real, intent(in) :: x
        real :: y

        y = x**2

    end function square

end module maths
```

Invalid:

```fortran
module maths

program my_program

end program my_program

end module maths
```

The main program must exist separately.

Program units cannot overlap.

A module cannot be defined inside a program.

---

# **Multiple Modules**

A program can use multiple modules.

```fortran
use constants
use maths
use geometry
```

This allows different parts of a large application to remain independent and organized.

A source file may also contain multiple modules.

---

# **Module Files (.mod)**

When a module is compiled, the compiler creates a file with the extension `.mod`.

For example:

```fortran
module maths
```

may generate:

```
maths.mod
```

This file contains information about:

- module procedures
- derived types
- module variables
- interfaces

When another source file contains:

```fortran
use maths
```

the compiler reads `maths.mod`.

For this reason, modules must usually be compiled before files that use them.

---

# **File Structure and Compilation**

A source file may contain multiple modules.

If a file contains a main program, the main program must be the final program unit in that file.

Compilation must follow module dependencies.

For example:

```
constants_module
        ↓
physics_module
        ↓
main program
```

The compiler must compile `constants_module` before `physics_module`, and both before the main program.

Module names and filenames do not need to match.

---

# Visibility Control

Modules can control which variables, types, and procedures are visible outside the module.

Common attributes include:

```fortran
public
private
```

`public` entities may be accessed by other program units.

`private` entities are only accessible inside the module.

Using `private` helps hide implementation details and reduces accidental misuse of data.

# **Complete Example**

Module:

```fortran
module maths

    implicit none

contains

    function square(x) result(y)

        real, intent(in) :: x
        real :: y

        y = x**2

    end function square

end module maths
```

Program:

```fortran
program example

    use maths

    implicit none

    real :: a

    a = square(4.0)

    print *, a

end program example
```

Output:

```
16.0
```

### **How It Works**

1. The module `maths` defines the function `square`.
2. The program imports the module using `use maths`.
3. The function becomes available inside the program.
4. The function is called.
5. The result is returned and printed.

---

# **Summary**

| Statement | Purpose |
| --- | --- |
| `module name` | Begin a module |
| `contains` | Separate declarations from procedures |
| `end module name` | End a module |
| `use module_name` | Import a module |
| Module procedure | Function or subroutine inside a module |
| Specification part | Variables, constants, types, interfaces |
| Procedure part | Functions and subroutines |
| Internal procedure | Procedure inside another procedure |
| External procedure | Procedure outside a module |
| `.mod` file | Compiler-generated module information |

---

# **Key Idea**

Modules are the primary tool for organizing modern Fortran programs.

They allow related variables, constants, derived types, functions, and subroutines to be grouped together and reused throughout a program.

Most large Fortran applications consist of a small main program and a collection of modules containing the actual functionality.