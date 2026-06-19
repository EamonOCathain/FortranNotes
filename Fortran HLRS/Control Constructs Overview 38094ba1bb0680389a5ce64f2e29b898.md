# Control Constructs Overview

Control constructs determine the **flow of execution** in a program. They allow code to:

- Repeat actions (**loops**)
- Make decisions (**conditionals**)
- Select between alternatives (**case selection**)

---

# **Conditional Execution (`if`)**

Execute code only when a condition is true.

```fortran
if (x > 0) then
    print *, "positive"
else if (x < 0) then
    print *, "negative"
else
    print *, "zero"
end if
```

Conditions are logical expressions that evaluate to `.true.` or `.false.`.

---

# **Case Selection (`select case`)**

Choose between multiple alternatives.

```fortran
select case(day)

case(1)
    print *, "Monday"

case(2)
    print *, "Tuesday"

case default
    print *, "Unknown"

end select
```

Useful when testing one variable against many possible values.

---

# **Counted Loops (`do`)**

Repeat a block a fixed number of times.

```fortran
do i = 1, 10
    print *, i
end do
```

General form:

```fortran
do variable = start, end, step
    ...
end do
```

Example:

```fortran
do i = 1, 20, 2
    print *, i
end do
```

prints odd numbers from 1 to 19.

---

# **Conditional Loops (`do while`)**

Repeat while a condition remains true.

```fortran
do while (x < 100.0)
    x = x * 2.0
end do
```

Useful when the number of iterations is unknown beforehand.

---

# **Loop Control**

### **Skip Current Iteration**

```fortran
cycle
```

Equivalent to Python’s `continue`.

```fortran
do i = 1, 10

    if (i == 5) cycle

    print *, i

end do
```

---

### **Exit Loop Early**

```fortran
exit
```

Equivalent to Python’s `break`.

```fortran
do i = 1, 100

    if (i > 10) exit

    print *, i

end do
```

---

# **Named Constructs**

Loops and conditionals can be given names.

```fortran
time_loop: do t = 1, 100

    if (condition) exit time_loop

end do time_loop
```

Useful when working with nested loops.

---

# **Block Structure**

Most Fortran control constructs use an opening and closing statement:

```fortran
if (...) then
    ...
end if
```

```fortran
do
    ...
end do
```

```fortran
select case(...)
    ...
end select
```

This makes program structure explicit and easy to read.

---

# **Associate**

`associate` creates a temporary alias (nickname) for a variable,
array section, structure component, or expression.

```fortran
associate( x => particle%position%x, &
           y => particle%position%y )

    r = sqrt(x**2 + y**2)

end associate
```

## **Program Termination (`stop` and `error stop`)**

Fortran provides statements to terminate program execution.

```fortran
stop
```

Ends the program normally.

```fortran
error stop "Input file missing"
```

Terminates the program because of a fatal error and prints a message. In parallel coarray programs, `error stop` terminates all images.

| **Construct** | **Purpose** |
| --- | --- |
| `stop` | Normal program termination |
| `error stop` | Terminate due to a fatal error |

Example

```fortran
if (.not. file_exists) then
    error stop "Configuration file not found"
end if

print *, "Running simulation..."
```

# **Common Control Constructs**

| **Construct** | **Purpose** |
| --- | --- |
| `if ... then` | Conditional execution |
| `else if` | Additional condition |
| `else` | Default branch |
| `select case` | Multi-way selection |
| `do` | Counted loop |
| `do while` | Conditional loop |
| `cycle` | Skip to next iteration |
| `exit` | Leave loop immediately |

---

# **Key Idea**

Control constructs do not perform calculations themselves. They control **when**, **how often**, and **under what conditions** calculations are executed.