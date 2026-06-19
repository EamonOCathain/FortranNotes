# Interfaces

## What is an Interface?

An **interface** tells the compiler how a procedure (function or subroutine) should be called.

It describes:

- The procedure name
- The number and order of arguments
- The type, kind, and rank (TKR) of each argument
- Whether arguments are input (`intent(in)`), output (`intent(out)`), or both (`intent(inout)`)
- The return type of a function

An interface acts as a contract between a procedure and the code that calls it.

Formally, an interface is the ordered set of dummy arguments of a procedure together with their associated types, kinds, ranks, attributes, and function result type.

An interface can either be the part of an existing procedure before the contains block:

Example:

```fortran
function square(x) result(y)

    real, intent(in) :: x
    real :: y

    y = x*x

end function square
```

The interface consists only of the procedure declaration and argument information, not the executable code. So only this part:

```fortran
function square(x) result(y)

    real, intent(in) :: x
    real :: y
```

Or it can be as a standalone interface block (more below):

```fortran
interface

    function square(x) result(y)

        real, intent(in) :: x
        real :: y

    end function square

end interface
```

---

# Why Interfaces Matter

Interfaces allow the compiler to understand how a procedure should be called.

Without an interface, the compiler has very little information available and therefore cannot perform many checks.

With an explicit interface, the compiler can verify:

- Number of arguments
- Types of arguments
- Array dimensions and ranks
- Function return types
- Optional arguments
- Keyword arguments

This catches many programming mistakes before the program is executed.

---

# Implicit and Explicit Interfaces

We distinguish between:

- Implicit interfaces
- Explicit interfaces
- Abstract interfaces (used later in object-oriented programming)

---

## Implicit Interfaces

An implicit interface occurs when the compiler does not know the full procedure declaration.

Typical cases include:

- External procedures
- Traditional FORTRAN 77 style code
- Library routines
- Calls to C procedures

Example:

```fortran
real :: x

x = my_function(5.0)
```

If the compiler only knows the name `my_function`, then the interface is implicit.

Characteristics:

- Traditional FORTRAN 77 style
- Common in older code bases
- Limited compile-time checking
- Compiler does not fully verify arguments
- Generally avoided in modern Fortran

---

## Explicit Interfaces

An explicit interface means the compiler knows the complete procedure declaration.

This occurs automatically for:

- Module procedures
- Internal procedures
- Procedures imported using `use`
- Intrinsic procedures

Example:

```fortran
module maths

contains

    real function square(x)

        real, intent(in) :: x

        square = x*x

    end function square

end module maths
```

When another program writes:

```fortran
use maths
```

the compiler automatically receives the complete interface information from the module.

This is one of the major reasons modern Fortran code is organised around modules.

---

# Why Modern Fortran Prefers Explicit Interfaces

Many modern Fortran features require the compiler to know the complete procedure declaration.

Examples include:

- Keyword arguments
- Optional arguments
- Generic procedures
- Assumed-shape arrays
- Functions returning arrays
- Functions returning variable-length character strings
- Allocatable and pointer arguments
- Operator overloading
- Overloaded assignment

Without an explicit interface, the compiler cannot correctly generate the information required to pass these objects between the caller and the procedure.

For example, an assumed-shape array:

```fortran
subroutine process(array)

    real, dimension(:,:) :: array

end subroutine process
```

requires the compiler to automatically pass shape information along with the array itself.

Similarly, keyword arguments require the compiler to know the names of the dummy arguments:

```fortran
call update(x=10.0, tolerance=1.0e-6)
```

Because the compiler knows the complete interface, it can verify:

- Types
- Kinds
- Dimensions
- User-defined types

This greatly reduces programming errors and simplifies development.

---

# Interface Blocks

An interface block is a mechanism for manually providing interface information.

Its primary purpose is to convert an implicit interface into an explicit interface.

Example:

```fortran
interface

    real function fun(x)

        real, intent(in) :: x

    end function fun

end interface
```

Important points:

- No executable code appears inside the interface block.
- Only dummy arguments are declared.
- Local variables are omitted.
- The interface block is essentially a copy of the procedure header and argument declarations.

Historically, interface blocks were often used to provide explicit interfaces for external procedures.

Today, placing procedures inside modules is usually preferred.

---

# Generic Interfaces

A generic interface allows several procedures to share a single public name.

The compiler chooses the correct implementation based on the arguments supplied.

Example:

```fortran
interface area

    module procedure circle_area
    module procedure rectangle_area

end interface
```

Now both procedures can be called using the same name:

```fortran
a = area(radius)

a = area(length, width)
```

The compiler examines the argument list and selects the correct procedure.

This is similar to function overloading in languages such as C++ or Java.

## Generic Interfaces Example

Generic interfaces are often used to create intuitive APIs.

Example:

```fortran
interface print

    module procedure print_tabular

end interface
```

The user simply writes:

```fortran
call print(tabular)
```

without needing to know the underlying implementation procedure.

This helps make code easier to read and use.

---

# Key Takeaways

- An interface describes how a procedure is called.
- Explicit interfaces allow the compiler to verify procedure calls.
- Module procedures automatically provide explicit interfaces.
- Many modern Fortran features require explicit interfaces.
- Interface blocks can be used to add interfaces to external procedures.
- Generic interfaces allow multiple procedures to share a single public name.
- Interfaces can define new operators.
- Interfaces can overload existing operators and assignment.
- Modern Fortran strongly prefers module procedures over external procedures.
- Most modern Fortran programs obtain explicit interfaces automatically through modules.

# Advanced Interfaces

# User-Defined Operators

Interfaces can define entirely new operators.

Example:

```fortran
interface operator(.X.)

    module procedure cross_product

end interface
```

Procedure:

```fortran
function cross_product(a,b) result(c)

    real(kind=rk), dimension(3), intent(in) :: a,b
    real(kind=rk), dimension(3) :: c

end function cross_product
```

Usage:

```fortran
v3 = v1 .X. v2
```

This can make code resemble the underlying mathematics more closely.

Restrictions:

- Arguments must have `intent(in)`
- Operator names cannot contain digits
- Operator names cannot contain underscores
- User-defined operators have fixed low precedence

Because of the precedence rules, parentheses are often required:

```fortran
v3 = v2 + ((v1 .X. v2) .X. v2)
```

---

# Operator Overloading

Instead of creating a new operator, an existing operator may be overloaded for a custom type.

Example:

```fortran
interface operator(+)

    module procedure add_vector

end interface
```

Now:

```fortran
v3 = v1 + v2
```

may call:

```fortran
add_vector(v1,v2)
```

when the operands are of the appropriate derived type.

The compiler automatically chooses the correct implementation based on the argument types.

This allows user-defined types to behave naturally.

---

# Overloaded Assignment

Assignment can also be overloaded.

Example:

```fortran
interface assignment(=)

    module procedure assign_matrix_to_matrix

end interface
```

Now:

```fortran
matrix1 = matrix2
```

calls:

```fortran
assign_matrix_to_matrix(matrix1,matrix2)
```

instead of using the default assignment behaviour.

This is useful when assignment requires custom logic, such as copying dynamically allocated data structures.

# Modernising Legacy Code

Historically, procedures often lived in separate source files.

Example:

```fortran
subroutine up1(...)
...
end subroutine up1
```

```fortran
real function fun1(...)
...
end function fun1
```

called from:

```fortran
program my_prog

    implicit none

    real :: var
    real, external :: fun1

    call up1(var)
    var = fun1(47.)

end program my_prog
```

In this design:

- Procedures are external
- Interfaces are implicit
- Compiler checking is limited

---

## Option 1: Add Interface Blocks

A first improvement is to create a module containing interface blocks:

```fortran
module interface_module

    interface

        subroutine up1(...)
        end subroutine up1

    end interface

end module interface_module
```

This adds explicit interface checking without modifying the original procedures.

---

## Option 2: Move Procedures into Modules

A better solution is to place procedures directly inside modules:

```fortran
module my_module

contains

    subroutine up1(...)
    end subroutine up1

    real function fun1(...)
    end function fun1

end module my_module
```

The main program then becomes:

```fortran
program my_prog

    use my_module

    implicit none

end program my_prog
```

Benefits:

- Automatic explicit interfaces
- Better organisation
- Strong compiler checking
- Simpler maintenance

This is the recommended modern approach.

---