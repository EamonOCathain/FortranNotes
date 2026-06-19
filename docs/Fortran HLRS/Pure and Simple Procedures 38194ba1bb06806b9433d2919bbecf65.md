# Pure and Simple Procedures

# **Pure Procedures (`pure`)**

A pure procedure is a function or subroutine that has no side effects. Its result depends only on its input arguments, and it does not modify anything outside itself.

## **Why use pure procedures?**

- Easier to understand and debug.
- Safer for parallel execution.
- Allows the compiler to perform more optimizations.
- Makes code more predictable.

## **Restrictions**

A pure procedure cannot:

- Modify global/module variables.
- Modify its input arguments.
- Perform file I/O (`read`, `write`, `open`, etc.).
- Execute `stop` statements.
- Call impure procedures.

A pure subroutine may modify arguments declared with `intent(out)` or `intent(inout)`.

## **Example**

```fortran
pure function transpiration(gs, vpd) result(E)

    real, intent(in) :: gs, vpd
    real :: E

    E = gs * vpd

end function transpiration
```

This function simply computes a value from its inputs and has no side effects.

---

# **Simple Procedures (`simple`)**

A **simple procedure** is a newer Fortran 2023 feature that is even more restrictive than a pure procedure.

Relationship:

```
simple ⊂ pure
```

Every simple procedure is pure, but not every pure procedure is simple.

---

## **Why Were Simple Procedures Introduced?**

A pure procedure guarantees:

- no side effects
- no modification of global state
- predictable behaviour

However, a pure procedure may still **depend on data outside its argument list**.

For example:

```fortran
module constants

    real :: gravity = 9.81

contains

    pure function potential_energy(mass, height) result(pe)

        real, intent(in) :: mass, height
        real :: pe

        pe = mass * gravity * height

    end function potential_energy

end module constants
```

This function is pure because it does not modify anything.

However, it still depends on:

```fortran
gravity
```

which is not an argument.

A reader must inspect the surrounding module to understand all inputs.

---

## **Purpose of Simple Procedures**

A simple procedure removes these hidden dependencies.

The goal is that:

```
Output depends only on arguments
```

Everything required by the procedure should be visible in the argument list.

This makes the procedure:

- easier to reason about
- easier to test
- easier for the compiler to optimise
- safer for parallel execution

---

## **Pure vs Simple**

Pure procedure:

```fortran
pure function potential_energy(mass, height) result(pe)

    pe = mass * gravity * height

end function potential_energy
```

Uses:

```fortran
gravity
```

from outside the argument list.

Allowed for `pure`.

---

Simple procedure:

```fortran
simple function potential_energy(mass, height, gravity) result(pe)

    real, intent(in) :: mass
    real, intent(in) :: height
    real, intent(in) :: gravity

    pe = mass * gravity * height

end function potential_energy
```

Now every dependency is explicit.

---

## **Restrictions**

A simple procedure:

- Must satisfy all requirements of a pure procedure.
- Cannot rely on hidden state outside its arguments.
- Should obtain all required information through its argument list.
- Is intended for deterministic calculations whose outputs depend only on their inputs.

Conceptually:

```
pure:
    output = f(arguments, external constants)

simple:
    output = f(arguments)
```

---

## **Example**

```fortran
simple function square(x) result(y)

    real, intent(in) :: x
    real :: y

    y = x * x

end function square
```

The function:

- reads only its arguments
- has no side effects
- has no hidden dependencies

and is therefore both:

```
simple
pure
```

---

## **Should I Use Them?**

For most scientific modelling projects:

- Learn and use **pure procedures** first.
- Use **simple procedures** when you want every dependency to be explicitly passed through the argument list.
- Many existing Fortran codebases do not yet use `simple`, because it was introduced only in Fortran 2023.