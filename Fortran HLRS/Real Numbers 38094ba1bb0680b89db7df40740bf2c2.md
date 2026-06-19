# Real Numbers

## **Real Numbers**

```fortran
real :: x
```

Used for decimal numbers:

```fortran
x = 3.14
x = -0.001
```

### **Double Precision**

Legacy:

```fortran
double precision :: x
```

Preferred:

```fortran
integer, parameter :: dp = 8
real(kind=dp) :: x
```

### **Precision-Safe Constants**

```fortran
real(dp) :: x

x = 0.1_dp
```

The `_dp` suffix ensures the constant has the same precision as the variable.

## **Precision in Expressions**

The precision of a variable does **not** automatically increase the precision of constants used in an expression.

```fortran
integer, parameter :: dp = 8
real(dp) :: x

x = 0.1      ! lower-precision constant
x = 0.1_dp   ! matching precision
```

When working with high-precision variables, constants should use the same precision:

```fortran
y = x * 2.0_dp
```

instead of:

```fortran
y = x * 2.0
```

### **Rule of Thumb**

When using `real(dp)` variables, append `_dp` to floating-point constants:

```fortran
0.1_dp
2.0_dp
3.14159_dp
```

This avoids unnecessary precision loss from mixed-precision expressions.

The key idea is:

**Precision is determined by the entire expression, not just the variable receiving the result.**

# Precision Warning

- The precision of your expression can change your results
- If the number is smaller or larger than your precision then you may

### **Range ≠ Precision**

- **Range** = the amount of numbers which can be represented
- **Precision** = the amount of digits a number can have

e.g. 64 bit has 16 digits → 1234567890123456

Good catch. Those are **two different concepts**:

Think of scientific notation:

```
1.234567890123456 × 10^100
```

Double precision stores roughly:

- 15–16 significant digits (`1.234567890123456`)
- a huge exponent (`10^100`)

The exponent gives the **range**.

The significant digits give the **precision**.

### **Example**

Double precision can represent both:

```
1.234567890123456 × 10^-300
```

and

```
1.234567890123456 × 10^300
```

accurately to about 15–16 digits.

But it cannot distinguish:

```
1.234567890123456 × 10^300
```

from

```
1.2345678901234561 × 10^300
```

because that difference is beyond the available precision.