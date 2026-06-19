# Performance

One reason Fortran performs well in scientific computing is that the language makes strong assumptions about procedure arguments.

When a procedure is called, the compiler assumes that different arguments refer to different objects.

For example:

```fortran
call update(a, b, c)
```

the compiler assumes that `a`, `b`, and `c` are independent.

Similarly, for arrays:

```fortran
call update(A(1:5), A(3:9))
```

the compiler assumes that the two array sections do not overlap, even though they share elements.

Because of these assumptions, modifying overlapping arguments inside a procedure is prohibited.

---

The same idea applies to functions.

In an expression such as:

```fortran
a = fun1(x) * fun2(y)
```

the compiler assumes that the two functions are independent and may evaluate them in any order.

Therefore, functions should not modify variables that affect other functions appearing in the same expression.

---

These assumptions allow the compiler to:

- reorder calculations
- vectorize loops
- optimize memory access
- execute operations in parallel

For example:

```fortran
do i = 1, n
    c(i) = a(i) + b(i)
end do
```

Because the compiler assumes that `a`, `b`, and `c` do not overlap, it can process multiple elements simultaneously using vector instructions.

For this reason, procedures should avoid hidden side effects and should not modify overlapping arguments. Following these rules helps the compiler generate faster and more efficient code.

This keeps the key teaching point from the slides:

Fortran’s performance comes partly from the language guaranteeing things that C/C++ generally cannot assume, especially that procedure arguments are independent and functions do not interfere with one another.