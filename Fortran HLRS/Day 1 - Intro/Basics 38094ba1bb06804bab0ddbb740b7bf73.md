# Basics

# Basics

### **Basic Data Types**

Fortran provides several intrinsic data types for variables and constants:

- Integer numbers (`integer`)
- Floating-point numbers (`real`)
- Complex floating-point numbers (`complex`)
- Character/string data (`character`)

Example:

```fortran
integer :: i = 5
real :: x = 3.14
complex :: z = (1.0, 2.0)
character(len=20) :: name = "Brian"
```

---

### **Variables and Operations**

- Variable declarations
- Procedures operating on these data types
- Assignments:

```fortran
a = b
```

(content of `a` ← content of `b`)

Example:

```fortran
integer :: a, b

b = 10
a = b
```

---

### **Operators**

Arithmetic operators:

```fortran
+  -  *  /  **
```

Example:

```fortran
x = 2 + 3
y = 4 * 5
z = 2 ** 3
```

Logical operators:

```fortran
.and.  .or.  .not.
```

Example:

```fortran
if (a > 0 .and. b > 0) then
    print *, "Both positive"
end if
```

---

### **Arrays**

For storing indexed data:

```fortran
a(ii,jj)
```

Example:

```fortran
real :: a(3,3)

a(2,3) = 5.0
```

---

## **Control Flow Constructs**

Used to influence the flow of execution.

### **Loops**

```fortran
do ... end do
```

Example:

```fortran
do i = 1, 5
    print *, i
end do
```

```fortran
do concurrent ... end do
```

Example:

```fortran
do concurrent (i = 1:5)
    a(i) = i**2
end do
```

---

### **Branching**

```fortran
if ... then ... else ... end if
```

Example:

```fortran
if (x > 0) then
    print *, "Positive"
else
    print *, "Not positive"
end if
```

or

```fortran
select case ... end select
```

Example:

```fortran
select case (day)
case (1)
    print *, "Monday"
case (2)
    print *, "Tuesday"
case default
    print *, "Other day"
end select
```

## **Program Structure**

Simple programs become difficult to maintain as they grow larger.

To build large programs, code is split into **small program units** that:

- Perform a specific task
- Have a limited variable scope
- Are easier to test and debug
- Can be reused in multiple places

### **Procedures**

### **Function**

Returns a value.

```fortran
function square(x)
    square = x*x
end function square
```

### **Subroutine**

Performs a task without returning a value directly.

```fortran
subroutine print_message()
    print *, "Hello"
end subroutine print_message
```

---

## **User-Defined Data Types**

For storing more complex data structures.

```fortran
type :: Particle
    real :: x
    real :: y
    real :: mass
end type Particle
```

---

## **Modules**

Used to group related data and procedures together.

```fortran
module math_utils
contains
    function square(x)
        square = x*x
    end function square
end module math_utils
```

## **Character Set and Basic Syntax**

**Names**

- Names may contain letters, numbers, and `_`
- Names must start with a letter
- Up to 63 characters
- Fortran is not case-sensitive
- Usually one statement per line
- Multiple statements can be separated by `;`

```fortran
a = 0.; b = 2.0*a
```

- Source forms:
    - `.f` = fixed form (old)
    - `.f90` = free form (modern)
- Labels can be used as branch targets

```fortran
if (a < 0.) goto 1000
1000 continue
```

- `::` separates the type from variable names

```fortran
real :: a, b
integer :: n
```

## Example

**Full:**

```python
call sparse_matrix_solver(matrix,rhs,LHS,n_max)
a = 0.; b = 2.*a
if ( a < 0.) goto 1000 ! not recommended
r = exp(b) 
1000 continue
```

Call a subroutine.

```fortran
call sparse_matrix_solver(matrix,rhs,LHS,n_max)
```

Two assignments on one line.

```fortran
a = 0.; b = 2.*a
```

Jump to label `1000` if condition is true.

```fortran
if (a < 0.) goto 1000
```

Compute e^b.

```fortran
r = exp(b)
```

Label marking a possible jump destination.

```fortran
1000 continue
```

### **Free Source Form (Modern Fortran)**

- Standard source format (`.f90` files)
- Up to 132 characters per line (much higher in modern standards)
- Multiple statements can be separated by `;`
- Comments start with `!`
- Continue a statement onto the next line with `&`
- Tabs are not required
- Indentation improves readability but has no effect on execution (unlike Python)

### Example

```fortran
program centigrade_to_fahrenheit

implicit none

! This program converts a Centigrade temperature (C)
! to Fahrenheit (F) via the formula F = 9/5 * C + 32

real :: deg_fah, deg_cen

write(*,*) 'What is the Centigrade temperature?'
read(*,*) deg_cen

deg_fah = (9.0*deg_cen/5.0) + 32.0

write(*,*) 'Centigrades:', deg_cen, 'Fahrenheit:', deg_fah

end program centigrade_to_fahrenheit
```

- `program ... end program` defines the start and end of the program.
- `implicit none` requires all variables to be declared explicitly.
- `!` denotes a comment.
- `real :: deg_fah, deg_cen` declares two floating-point variables.
- `write(*,*)` prints output to the screen.
- `read(*,*)` reads input from the keyboard.
- `deg_fah = ...` performs the Celsius → Fahrenheit conversion.
- The final `write(*,*)` displays the input and calculated result.

### Example 2

```fortran
program simple_IO_program

! all variables must be declared
implicit none

! set the variable types
integer :: jj
real :: rr
real, allocatable, dimension(:) :: arr
integer :: my_unit
integer :: ll

! Setting values
jj = 101
rr = 1.234

! Define Arrays - Here a one dimensionsal arrays with numbers 1-5 multiplied by 0.1
arr = [(0.1*real(ll), ll=1,5)]

! 
open(newunit=my_unit, file='my_file.dat') ! connection to an exteranl file

! This defines how to print the results
write(my_unit,'(a,i0,a,f0.3,a,*(e0.2,1x))') 'jj=',jj,' rr=',rr,' arr=',arr ! a,i0,f0.3,e0.2 are edit descriptors

end program simple_IO_program
```

- `allocatable, dimension(:)` declares a dynamically-sized 1D array.
- `arr = [(0.1*real(ll), ll=1,5)]` creates the array `[0.1, 0.2, 0.3, 0.4, 0.5]`.
- `open(...)` opens a file and associates it with a unit number (`my_unit`).
- `write(my_unit, ...)` writes data to the file rather than to the screen.
- The format string (`'(a,i0,a,f0.3,a,*(e0.2,1x))'`) controls how the output is printed.
- Result written to `my_file.dat`:

**So to explain the printing variables line in more detail:**

*The assignment of the variables - left defines variable type and right define the order* 

```fortran
write(my_unit,'(a,i0,a,f0.3,a,*(e0.2,1x))') 'jj=',jj,' rr=',rr,' arr=',arr
```

| **Descriptor** | **Meaning** |
| --- | --- |
| `a` | Character string |
| `i0` | Integer (minimum width) |
| `f0.3` | Fixed-point real with 3 decimal places |
| `e0.2` | Scientific notation with 2 decimal places |
| `1x` | Insert one blank space |
| `*(...)` | Repeat format for all remaining array elements |

```
a      -> 'jj='
i0     -> jj

a      -> ' rr='
f0.3   -> rr

a      -> ' arr='
*(e0.2,1x) -> every element of arr
```

Output:

```
jj=101 rr=1.234 arr=0.10E+0 0.20E+0 0.30E+0 0.40E+0 0.50E+0
```

## Example 3

*Programme to cancel multiple lines*

*Overall, the program reads a file line-by-line and writes only lines that differ from the immediately preceding line.*

```fortran
program cancel_multiple_lines

    implicit none      ! all variables have to be declared

    character(len=131) :: line,old_line     ! string variables
    integer            :: in_unit, out_unit ! integer variables

    open(newunit=in_unit,file='input')
    open(newunit=out_unit,file='output')    ! connection to files

    old_line=' '

    do      ! endless loop

        ! we will leave to 1000 continue at the end of the file

        read(in_unit,'(a)',end=1000) line   ! read line ; at end of file goto 1000

        if(line /= old_line) then

            ! we write only the new lines with a preamble

            write(out_unit,*) 'fx -c ',trim(line)

        end if

        old_line=line

    end do      ! end of loop

1000 continue

end program cancel_multiple_lines
```

- `character(len=131)` declares two strings, `line` and `old_line`.
- `open(...)` opens the input and output files and assigns unit numbers.
- `old_line=' '` initializes `old_line` to a blank string.
- `do ... end do` creates an infinite loop.
- `read(..., end=1000)` reads one line from the input file; when the end of the file is reached, execution jumps to label `1000`.
- `if(line /= old_line)` checks whether the current line differs from the previous line (`/=` means “not equal”).
- `trim(line)` removes trailing spaces from the string.
- `write(out_unit,*)` writes the line to the output file, prefixed with `"fx -c "`.
- `old_line=line` stores the current line for comparison during the next iteration.
- `1000 continue` is the destination reached when the end of the input file is encountered.
- Overall, the program reads a file line-by-line and writes only lines that differ from the immediately preceding line.

## Example 3 - ASCII

```fortran
program ascii

implicit none

integer :: ii, imax

integer :: out_unit

open(newunit=out_unit,file='ascii.dat')

imax=128

do ii=1,imax      ! loop with control variables

    write(out_unit,'(a,i0,a,a)') 'char(',ii,')=',char(ii)

end do

end program ascii
```

- `integer :: ii, imax` declares loop variables.
- `integer :: out_unit` stores the file unit number.
- `open(...)` opens the file `ascii.dat` for writing.
- `imax=128` sets the upper limit of the loop.
- `do ii=1,imax` loops from 1 to 128.
- `char(ii)` converts the integer `ii` into its corresponding ASCII character.
    - inbuilt fortran function
- `write(...)` writes the integer and character to the file.
- `end do` ends the loop.
- The program creates a file containing the ASCII table.