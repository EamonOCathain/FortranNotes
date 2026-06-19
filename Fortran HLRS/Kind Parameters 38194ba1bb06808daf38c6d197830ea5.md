# Kind Parameters

## **What is a Kind?**

A **kind parameter** specifies the precision and storage size used for a data type.

Kinds can be applied to:

- `integer`
- `real`
- `complex`
- (less commonly) `logical` and `character`

---

## **Integer Kinds**

The intrinsic function:

```fortran
selected_int_kind(r)
```

returns an integer kind capable of representing at least:

```
-10^r < n < 10^r
```

Example:

```fortran
integer, parameter :: ik = selected_int_kind(9)
integer(kind=ik) :: i
```

creates an integer capable of storing at least 9 decimal digits.

---

## **Real Kinds**

The intrinsic function:

```fortran
selected_real_kind(p, r)
```

returns a real kind with:

- at least `p` decimal digits of precision
- at least `r` decimal exponent range

Example:

```fortran
integer, parameter :: rk = selected_real_kind(15, 307)
real(kind=rk) :: x
```

gives approximately double precision accuracy.

---

## **The `kind()` Function**

The intrinsic function:

```fortran
kind(variable)
```

returns the kind value of a variable.

Examples:

```fortran
kind(0)      ! default integer
kind(0.0)    ! default real
kind(0.0d0)  ! double precision real
```

Useful when writing generic or portable code.

---

## **Precision and Range**

Two useful inquiry functions are:

```fortran
precision(x)
```

Returns the number of significant decimal digits.

```fortran
range(x)
```

Returns the decimal exponent range.

---

## **Modern Fortran Recommendation**

Rather than using `selected_int_kind()` and `selected_real_kind()` throughout a program, modern Fortran recommends:

```fortran
use iso_fortran_env
```

which provides standard kinds:

```fortran
int8
int16
int32
int64

real32
real64
real128
```

Example:

```fortran
use iso_fortran_env

real(real64)   :: psi_x
real(real64)   :: psi_l
integer(int32) :: timestep
```

---

## **Typical Scientific Computing Practice**

For numerical models (hydrology, ecology, climate models, ODE solvers):

```fortran
use iso_fortran_env

integer, parameter :: rk = real64

real(rk) :: psi_x
real(rk) :: psi_l
real(rk) :: dt
```

This provides approximately:

- 15–16 significant decimal digits
- exponent range of roughly ±10^307

and is the standard choice for most scientific applications.