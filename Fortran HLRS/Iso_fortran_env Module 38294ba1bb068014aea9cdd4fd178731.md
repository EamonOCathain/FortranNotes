# Iso_fortran_env Module

`iso_fortran_env` is an **intrinsic module** provided by every modern Fortran compiler.

It contains standardized definitions for data types, input/output streams, system constants, and compiler information.

Using `iso_fortran_env` helps make programs portable between different compilers and operating systems.

```fortran
use iso_fortran_env
```

---

# **Why Use `iso_fortran_env`?**

Different computers and compilers may store integers and floating-point numbers differently.

For example:

```fortran
integer
real
```

do not guarantee a particular size.

`iso_fortran_env` provides standard names for common sizes so that programs behave consistently across systems.

---

# **Portable Data Types**

The module provides kind values for common integer sizes:

```fortran
int8
int16
int32
int64
```

Example:

```fortran
use iso_fortran_env

integer(int32) :: year
integer(int64) :: population
```

Approximate ranges:

| **Kind** | **Typical Range** |
| --- | --- |
| int8 | −128 to 127 |
| int16 | −32,768 to 32,767 |
| int32 | ±2 billion |
| int64 | ±9×10¹⁸ |

---

The module also provides floating-point kinds:

```fortran
real32
real64
real128
```

Example:

```fortran
real(real64) :: pi
```

Typical meanings:

| **Kind** | **Common Name** |
| --- | --- |
| real32 | Single precision |
| real64 | Double precision |
| real128 | Quadruple precision (if supported) |

For scientific computing, `real64` is often the default choice.

---

# **Using Kinds for Constants**

When using kind parameters, numeric constants should normally use the same kind.

Example:

```fortran
real(real64) :: x

x = 3.141592653589793_real64
```

This ensures that the constant is stored with the correct precision.

---

# **Standard Input and Output Units**

The module provides predefined unit numbers:

```fortran
input_unit
output_unit
error_unit
```

These represent:

| **Constant** | **Purpose** |
| --- | --- |
| input_unit | Standard input (keyboard) |
| output_unit | Standard output (screen) |
| error_unit | Standard error output |

---

## **Input**

Instead of:

```fortran
read(*,*) x
```

you may write:

```fortran
read(input_unit,*) x
```

Both usually read from the keyboard.

---

## **Output**

Instead of:

```fortran
print *, "Hello"
```

you may write:

```fortran
write(output_unit,*) "Hello"
```

Both usually write to the terminal.

---

## **Error Output**

Errors can be written separately:

```fortran
write(error_unit,*) "Invalid input"
```

Many operating systems display standard output and error output separately.

This becomes useful when writing large scientific programs.

---

# **Compiler Information**

The module provides information about how a program was compiled.

---

## **`compiler_version()`**

Returns the compiler version.

Example:

```fortran
print *, compiler_version()
```

Possible output:

```
GNU Fortran 14.2.0
```

Useful when reporting software versions or debugging.

---

## **`compiler_options()`**

Returns the compiler flags used during compilation.

Example:

```fortran
print *, compiler_options()
```

Possible output:

```
-O3 -march=native -fopenmp
```

Useful when documenting exactly how a program was built.

---

# **Source Code Information**

Several functions provide information about the current source code location.

These are mainly used for debugging.

---

## **`source_file()`**

Returns the name of the source file.

Example:

```fortran
print *, source_file()
```

Possible output:

```
physics_model.f90
```

---

## **`source_line()`**

Returns the current source line number.

Example:

```fortran
print *, source_line()
```

Possible output:

```
127
```

---

## **`procedure_name()`**

Returns the name of the current procedure.

Example:

```fortran
print *, procedure_name()
```

Possible output:

```
compute_flux
```

---

## **Example Debug Message**

These functions can be combined:

```fortran
write(error_unit,*) &
    "Error in ", procedure_name(), &
    " (", source_file(), ":", source_line(), ")"
```

Possible output:

```
Error in compute_flux (physics_model.f90:127)
```

This makes locating errors much easier.

---

# **System Storage Information**

The module also provides information about storage sizes.

Examples:

```fortran
character_storage_size
numeric_storage_size
file_storage_size
```

These return the number of bits used for character, numeric, and file storage units on the current system.

Most programmers rarely need these values directly.

---

# **End-of-File Constants**

The module provides special values used during file reading:

```fortran
iostat_end
iostat_eor
```

These can be used when checking the result of a file read operation.

Example:

```fortran
integer :: status

read(unit, *, iostat=status) x

if (status == iostat_end) then
    print *, "End of file reached"
end if
```

---

# **What You Will Use Most Often**

In everyday scientific programming, the most commonly used features are:

```fortran
use iso_fortran_env
```

```fortran
int32
int64
real64
```

```fortran
input_unit
output_unit
error_unit
```

The compiler and source-code information functions are mainly useful for debugging, while many of the other features are intended for advanced applications and system programming.

---

# **Summary**

`iso_fortran_env` is a standard intrinsic module that provides:

- portable integer and floating-point kinds
- standard input, output, and error streams
- compiler information
- source-code location information
- file and storage constants
- various system-dependent definitions

It is one of the most useful intrinsic modules in modern Fortran and is commonly included in scientific programs.

This is about the level I’d expect in a second-year scientific programming course: enough detail to understand what the names mean without getting into compiler implementation details or coarray parallelism.