# Subroutines

# **Subroutines**

A **subroutine** is a procedure that performs a task when called by a program or another procedure.

Unlike a function, a subroutine does not return a single value. Instead, it can modify one or more variables through its argument list.

Subroutines are commonly used to:

- perform calculations
- update variables
- read or write files
- organize code into reusable blocks

---

# **Basic Structure**

General form:

```fortran
subroutine subroutine_name(argument_list)

    implicit none

    ! declarations

    ! executable statements

end subroutine subroutine_name
```

A subroutine is executed using the `call` statement:

```fortran
call subroutine_name(arguments)
```

---

# **Example**

```fortran
subroutine add_one(x)

    implicit none

    integer, intent(inout) :: x

    x = x + 1

end subroutine add_one
```

Called from a program:

```fortran
integer :: n

n = 5

call add_one(n)

print *, n
```

Output:

```
6
```

---

# **Arguments**

Subroutines receive information through an **argument list**.

```fortran
subroutine area(length, width, result)
```

The variables inside the subroutine are called **dummy arguments**.

```fortran
subroutine area(length, width, result)

    real :: length
    real :: width
    real :: result
```

The variables used when calling the subroutine are called **actual arguments**.

```fortran
call area(x, y, a)
```

The names do not have to match:

```fortran
subroutine area(length, width, result)

    result = length * width

end subroutine area
```

```fortran
call area(x, y, a)
```

Here:

```
length ↔ x
width  ↔ y
result ↔ a
```

Values are matched by position.

---

# **Intent Attributes**

Arguments should usually be given an intent.

## **`intent(in)`**

Input only.

```fortran
real, intent(in) :: length
```

The subroutine may use the value but cannot modify it.

---

## **`intent(out)`**

Output only.

```fortran
real, intent(out) :: area
```

The subroutine computes and returns a value.

---

## **`intent(inout)`**

Input and output.

```fortran
integer, intent(inout) :: x
```

The subroutine can read and modify the variable.

---

# **Example with Multiple Arguments**

```fortran
subroutine rectangle_area(length, width, area)

    implicit none

    real, intent(in)  :: length
    real, intent(in)  :: width
    real, intent(out) :: area

    area = length * width

end subroutine rectangle_area
```

Called as:

```fortran
real :: A

call rectangle_area(2.0, 4.0, A)

print *, A
```

Output:

```
8.0
```

---

# **Variable Scope**

Variables declared inside a subroutine only exist inside that subroutine.

```fortran
subroutine example()

    integer :: i

end subroutine example
```

The variable `i` is not visible outside the subroutine.

Likewise, variables from the calling program are not automatically available inside the subroutine unless:

- they are passed as arguments, or
- they are imported from a module.

---

# **Internal Subroutines**

Subroutines can be defined inside a program using `contains`.

```fortran
program example

    implicit none

    call hello()

contains

    subroutine hello()

        print *, "Hello"

    end subroutine hello

end program example
```

Internal subroutines can only be used by the program that contains them.

---

# **External and Module Subroutines**

For larger programs, subroutines are often placed in modules.

```fortran
module maths

contains

    subroutine add_one(x)

        integer, intent(inout) :: x

        x = x + 1

    end subroutine add_one

end module maths
```

Used as:

```fortran
use maths

call add_one(n)
```

This allows procedures to be reused across multiple programs.

---

# **Subroutines vs Functions**

| **Feature** | **Function** | **Subroutine** |
| --- | --- | --- |
| Returns a value | Yes | No |
| Called using | `y = f(x)` | `call procedure(...)` |
| Typical use | Equations and calculations | Tasks and updates |
| Number of outputs | One | Zero, one, or many |

Function:

```fortran
y = square(x)
```

Subroutine:

```fortran
call square(x, y)
```

---

# **Complete Example**

```fortran
program demo

    implicit none

    real :: length
    real :: width
    real :: area

    length = 5.0
    width  = 2.0

    call rectangle_area(length, width, area)

    print *, "Area =", area

contains

    subroutine rectangle_area(length, width, area)

        real, intent(in)  :: length
        real, intent(in)  :: width
        real, intent(out) :: area

        area = length * width

    end subroutine rectangle_area

end program demo
```

Output:

```
Area = 10.0
```

---

# **Summary**

| **Statement** | **Purpose** |
| --- | --- |
| `subroutine name(...)` | Begin a subroutine |
| `end subroutine name` | End a subroutine |
| `call name(...)` | Execute a subroutine |
| `intent(in)` | Input argument |
| `intent(out)` | Output argument |
| `intent(inout)` | Input and output argument |
| `contains` | Begin internal procedures |

---

# **Key Idea**

A subroutine is a reusable block of code that performs a task.

Values are passed into the subroutine through arguments, modified if necessary, and then returned to the calling program through output or input/output arguments.