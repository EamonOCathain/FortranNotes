# Complex Numbers

## **What is a Complex Number?**

A complex number has a **real part** and an **imaginary part**:

z = a + bi

where:

- `a` = real part
- `b` = imaginary part
- i = \sqrt{-1}

---

## **Declaration**

```fortran
complex :: z
complex :: z1, z2
```

Complex arrays can also be declared:

```fortran
complex, dimension(10) :: z_array
```

---

## **Precision**

Complex variables can use a kind parameter, just like real numbers.

```fortran
integer, parameter :: rk = 8

complex(kind=rk) :: z
```

The specified precision applies to both the real and imaginary parts.

---

## **Creating Complex Numbers**

Complex constants are written as:

```fortran
z = (3.0, 4.0)
```

which represents:

3 + 4i

Alternatively:

```fortran
z = cmplx(3.0, 4.0, kind=rk)
```

This form is preferred when the values come from variables.

---

## **Complex Arithmetic**

Standard arithmetic operators work as expected:

```fortran
+
-
*
/
**
```

Example:

```fortran
z1 = (1.0, 2.0)
z2 = (3.0, 4.0)

z3 = z1 + z2
```

---

## **Complex Functions**

Complex numbers can be used with intrinsic functions:

```fortran
z = sqrt((-1.0, 0.0))
```

Result:

z = i

---

## **Important Syntax Difference**

```fortran
z = (0, 5)
```

means:

0 + 5i

whereas

```fortran
z = (0.5)
```

means:

0.5 + 0i

The comma is significant.

---

## **Internal Representation**

A complex variable is stored as two consecutive real numbers:

```
(real part, imaginary part)

(3.0, 4.0)
```

Conceptually:

```
z
├── real part = 3.0
└── imaginary part = 4.0
```

---

### Accessing Real and Imaginary Parts

```fortran
cpl%re    ! real part (Fortran 2008)
cpl%im    ! imaginary part (Fortran 2008)
```

## **Summary**

| **Syntax** | **Meaning** |
| --- | --- |
| `complex :: z` | Complex variable |
| `complex(kind=rk) :: z` | Complex variable with specified precision |
| `z = (a,b)` | Complex number a + bi |
| `cmplx(a,b)` | Create a complex number |
| `real(z)` | Real part |
| `aimag(z)` | Imaginary part |
| `conjg(z)` | Complex conjugate |
| `abs(z)` | Magnitude |

For most scientific and environmental modelling work, you’ll rarely use complex numbers directly, but they are common in signal processing, Fourier transforms, quantum mechanics, and numerical linear algebra.