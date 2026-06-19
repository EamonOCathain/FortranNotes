# Main Program

# **Programs**

A Fortran program begins with a `program` statement and ends with an `end program` statement.

```fortran
program hello
    implicit none

    print *, "Hello World"

end program hello
```

A program is the top-level unit that controls execution.

Every executable Fortran application must have **exactly one main program**.

---

# **Program Structure**

A typical program contains:

1. Program declaration
2. `implicit none`
3. Module imports (`use`)
4. Variable declarations
5. Executable statements
6. Optional `contains` section for internal procedures

General structure:

```fortran
program program_name

    implicit none

    ! Module imports

    ! Variable declarations

    ! Executable statements

contains

    ! Internal functions and subroutines

end program program_name
```

Execution begins at the first executable statement and proceeds from top to bottom unless control structures alter the flow.

---

# **`implicit none`**

Always place:

```fortran
implicit none
```

near the top of every:

- program
- module
- subroutine
- function

This forces all variables to be declared explicitly and helps catch typing mistakes.

Without `implicit none`:

```fortran
x = 5
xx = 10
```

a typo could silently create a new variable.

With `implicit none`, the compiler reports an error.

---

# **Importing Modules**

Use the `use` statement to access variables, types, functions, and subroutines from another module.

```fortran
use my_module
```

Modules help organize larger programs and avoid repeating code.

---

# **Variable Declarations**

Variables must be declared before they are used.

```fortran
integer :: i
real :: x
character(len=20) :: name
```

Variables can then be assigned values:

```fortran
i = 5
x = 2.5
name = "Brian"
```

---

# **Executable Statements**

After declarations come executable statements.

```fortran
integer :: i

i = 5
print *, i
```

Statements are executed sequentially:

```fortran
program flow

    implicit none

    integer :: x

    x = 5
    print *, x

    x = x + 1
    print *, x

end program flow
```

Output:

```
5
6
```

---

# **Printing Output**

Use `print *` for simple output.

```fortran
print *, "Hello"
print *, x
print *, "Value =", x
```

Output is written to the terminal.

---

# **Reading Input**

Use `read *` to read values from the keyboard.

```fortran
integer :: n

read *, n
print *, n
```

Example input:

```
10
```

Output:

```
10
```

---

# **Internal Procedures and `contains`**

A program may contain internal functions and subroutines.

The keyword:

```fortran
contains
```

separates the executable section of the program from any internal procedures.

Example:

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

The `contains` section is optional.

Internal procedures can only be used within the program that contains them.

---

# **Command-Line Arguments**

Modern Fortran (2003+) allows access to command-line arguments.

Example:

```bash
./my_program input.txt
```

Arguments can be read using:

```fortran
character(len=100) :: filename

call get_command_argument(1, filename)
```

This is commonly used for configuration files and model input files.

---

# **Comments**

Anything after `!` is ignored by the compiler.

```fortran
! This is a comment

x = 5     ! Assign a value
```

Comments are useful for documenting code and explaining calculations.

---

# **Summary**

| **Statement** | **Purpose** |
| --- | --- |
| `program name` | Begin a program |
| `end program name` | End a program |
| `implicit none` | Require explicit declarations |
| `use module_name` | Import a module |
| `::` | Declare variables |
| `print *` | Write output |
| `read *` | Read input |
| `contains` | Begin internal procedures |
| `get_command_argument()` | Read command-line arguments |
| `!` | Comment |

---

# **Key Idea**

The main program is the entry point of a Fortran application.

A typical workflow is:

1. Import modules.
2. Declare variables.
3. Read inputs.
4. Perform calculations.
5. Write outputs.
6. Call functions and subroutines as needed.

Every executable Fortran application has exactly one main program, which coordinates the execution of the entire code.

### Example

```fortran
program example

    implicit none

    integer :: x

    x = 5

    print *, "Initial value =", x

    call add_one(x)

    print *, "New value =", x

contains

    subroutine add_one(n)

        integer, intent(inout) :: n

        n = n + 1

    end subroutine add_one

end program example
```