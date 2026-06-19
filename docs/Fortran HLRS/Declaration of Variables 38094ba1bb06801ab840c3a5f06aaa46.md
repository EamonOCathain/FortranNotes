# Declaration of Variables

## Implicit None

Always disable implicit typing:

```fortran
implicit none
```

This forces every variable to be explicitly declared and prevents accidental mistakes.

This is best practice

## **Variable Declaration**

Variables must be declared before use.

```fortran
integer :: i
real :: x
logical :: done
character(len=20) :: name
```

General syntax:

```fortran
data_type, attributes :: variable_list
```

Example:

```fortran
real, dimension(10) :: x
integer :: i
```

---

## **Variable Assignment**

Assign values using `=`.

```fortran
integer :: i
real :: x

i = 5
x = 3.14
```

The expression on the right-hand side is evaluated first and then assigned to the variable on the left.

```fortran
integer :: n
n = 3 + 4      ! n = 7
```

### **Type Conversion During Assignment**

```fortran
real :: x, y
integer :: n

x = 2.7
y = 5.6

n = x + y      ! n = 8
```

The result `8.3` is converted to an integer before assignment.

---

## **Intrinsic Data Types**

| **Type** | **Purpose** |
| --- | --- |
| `INTEGER` | Whole numbers |
| `REAL` | Floating-point numbers |
| `COMPLEX` | Complex numbers |
| `LOGICAL` | `.true.` / `.false.` |
| `CHARACTER` | Text strings |

---

## **Attributes**

Attributes modify how variables behave.

| **Attribute** | **Purpose** |
| --- | --- |
| `DIMENSION` | Array |
| `ALLOCATABLE` | Dynamic array |
| `POINTER` | Pointer |
| `TARGET` | Can be pointed to |
| `PARAMETER` | Constant |
| `INTENT` | Procedure argument usage |
| `OPTIONAL` | Optional procedure argument |

Example:

```fortran
integer, parameter :: n = 100
real, allocatable :: A(:,:)
```

---

---

## **Precision and Kind**

The `kind` parameter controls storage size and numerical precision.

```fortran
integer(kind=8) :: i
real(kind=8) :: x
```

A common pattern is:

```fortran
integer, parameter :: dp = 8
real(kind=dp) :: x
```

---