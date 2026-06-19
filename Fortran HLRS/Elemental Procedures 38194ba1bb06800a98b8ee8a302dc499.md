# Elemental Procedures

# **Elemental Procedures (`elemental`)**

An **elemental procedure** is a special type of procedure that is written for **scalar arguments** but can automatically operate on **arrays**.

The compiler applies the procedure independently to every element of the array.

---

## **Purpose**

Elemental procedures allow you to write a calculation once:

```fortran
f(x)
```

for a single value,

and then automatically apply it to:

```fortran
x(:)
```

without writing explicit loops.

This makes code:

- shorter
- easier to read
- easier to parallelise
- closer to mathematical notation

---

## **How It Works**

Suppose we define:

```fortran
elemental function square(x) result(y)

    real, intent(in) :: x
    real :: y

    y = x * x

end function square
```

The function is written for a single number:

```fortran
y = square(3.0)
```

which returns:

```
9.0
```

---

The same function can also be applied to an array:

```fortran
x = [1.0, 2.0, 3.0]

y = square(x)
```

The compiler automatically performs:

```
y(1) = square(x(1))
y(2) = square(x(2))
y(3) = square(x(3))
```

Result:

```
y = [1.0, 4.0, 9.0]
```

No explicit loop is required.

---

## **Conceptual View**

An elemental procedure behaves as if the compiler generates:

```fortran
do i = 1, size(x)

    y(i) = square(x(i))

end do
```

behind the scenes.

---

## **Restrictions**

An elemental procedure:

- Must be a function or pure subroutine.
- Must be `pure`.
- Dummy arguments must be scalar.
- The compiler handles array application automatically.
- Cannot rely on side effects.

Because every array element is treated independently, elemental procedures are naturally parallelisable.

---

## **Why Are Elemental Procedures Useful?**

Without `elemental`:

```fortran
do i = 1, n

    y(i) = square(x(i))

end do
```

With `elemental`:

```fortran
y = square(x)
```

The code is cleaner and easier to read.

---

## **Elemental vs Pure**

Relationship:

```
elemental ⊂ pure
```

Every elemental procedure is pure.

Not every pure procedure is elemental.

A pure procedure may operate on whole arrays, derived types, or more complex data structures.

An elemental procedure must be written so that each element can be evaluated independently.

---

## **When Should I Use Elemental Procedures?**

Use `elemental` when:

- The same calculation is applied independently to many values.
- There are no interactions between elements.
- The procedure is naturally vectorisable.
- You want the compiler to handle looping over arrays automatically.

Good candidates:

```fortran
elemental function square(...)
elemental function cube(...)
elemental function logistic(...)
elemental function fahrenheit_to_celsius(...)
```

Less suitable:

```fortran
read_data(...)
write_output(...)
sort_array(...)
```

because these involve file I/O, interactions between elements, or operations on entire datasets.

---

## **Summary**

```
ordinary procedure
    works on exactly what you pass

pure procedure
    no side effects

simple procedure
    no side effects and no hidden dependencies

elemental procedure
    pure procedure that automatically works on arrays element-by-element
```

Elemental procedures are useful for mathematical operations that should work identically on both single values and arrays, allowing concise, readable, and efficient code.