# Arithmetic Basics

## **Arithmetic with INTEGER and REAL**

```fortran
real :: a, b
integer :: ic, id

ic = 1
id = 3
b  = 3.0

a = b * ic / id    ! a = 1.0
a = ic / id * b    ! a = 0.0

a = 2.7
b = 5.6
ic = a + b         ! ic = 8
```

### **Integer Division**

When both operands are integers, the result is an integer:

```fortran
3 / 4    = 0
-7 / 4   = -1
```

The fractional part is discarded.

---

### **Order of Operations Matters**

Fortran evaluates multiplication and division **left to right**.

```fortran
a = b * ic / id
```

becomes:

```
3.0 * 1 = 3.0
3.0 / 3 = 1.0
```

Result:

```fortran
a = 1.0
```

But:

```fortran
a = ic / id * b
```

becomes:

```
1 / 3 = 0      ! integer division
0 * 3.0 = 0.0
```

Result:

```fortran
a = 0.0
```

---

### **Assignment Conversion**

If the types are mixed e.g. arithmetic of real numbers assigned to integer variable…

The expression on the right-hand side is evaluated first, then converted to the type of the left-hand side.

```fortran
a = 2.7
b = 5.6

ic = a + b
```

```
a + b = 8.3
```

Stored as integer:

```
ic = 8
```

---

### **Type Conversion Functions**

Common intrinsic conversion functions:

```fortran
int(x)     ! real -> integer
nint(x)    ! nearest integer
real(x)    ! integer -> real
dble(x)    ! to double precision
cmplx(x)   ! to complex
```

---

### **Rule of Thumb**

When division should produce a decimal result, ensure at least one operand is a `REAL`:

```fortran
real_result = real(ic) / id
```

instead of:

```fortran
real_result = ic / id
```

which performs integer division first.

## **Modulus (`mod`)**

Returns the **remainder after integer division**.

```fortran
integer :: ia, ib, ic

ia = 21
ib = 4

ic = mod(ia, ib)
```

Result:

```
ic = 1
```

because:

```
21 = 5×4 + 1
```

### **Testing Divisibility**

*Use modulus to test if a number is divisible*

```fortran
if (mod(ia, ib) == 0) then
    ! ib divides ia exactly
end if
```

Example:

```fortran
mod(21,3) = 0
mod(21,4) = 1
```

This is commonly used when checking factors, prime numbers, and divisibility.