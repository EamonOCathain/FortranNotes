# Operators

Operators are used to build expressions and assign values to variables.

```fortran
a = b * c + 1.0 + sin(x)

r = a * b
```

**Note:** `=` means **assignment**, not mathematical equality.

The expression on the right-hand side is evaluated first, then assigned to the variable on the left.

---

## **Arithmetic Operators**

| **Operator** | **Meaning** |
| --- | --- |
| `+` | Addition |
| `-` | Subtraction |
| `*` | Multiplication |
| `/` | Division |
| `**` | Exponentiation |
| `=` | Assignment |

Example:

```fortran
real :: a, b, c

a = 2.0
b = 3.0

c = a + b
c = a * b
c = a**2
```

---

## **Relational Operators**

Used to compare values and produce a logical result (`.true.` or `.false.`).

| **Modern** | **Older** | **Meaning** |
| --- | --- | --- |
| `==` | `.eq.` | Equal |
| `/=` | `.ne.` | Not equal |
| `<` | `.lt.` | Less than |
| `<=` | `.le.` | Less than or equal |
| `>` | `.gt.` | Greater than |
| `>=` | `.ge.` | Greater than or equal |

Example:

```fortran
logical :: cond

cond = a > 0.0
cond = a == b
```

Strings can also be compared:

```fortran
cond = trim(ch1) == "help"
```

---

## **Logical Operators**

Used to combine logical expressions.

| **Operator** | **Meaning** |
| --- | --- |
| `.and.` | AND |
| `.or.` | OR |
| `.not.` | NOT |
| `.eqv.` | Equivalent |
| `.neqv.` | Not equivalent |

Example:

```fortran
if (a > 0.0 .and. b > 0.0) then
    print *, "Both positive"
end if
```

---

## **Operator Precedence**

Operations are evaluated according to precedence rules.

Highest precedence first:

| **Precedence** | **Operators** |
| --- | --- |
| 1 | `()` |
| 2 | `**` |
| 3 | `*`, `/` |
| 4 | `+`, `-` |
| 5 | `//` |
| 6 | Relational (`<`, `==`, `>=`, etc.) |
| 7 | `.not.` |
| 8 | `.and.` |
| 9 | `.or.` |
| 10 | `.eqv.`, `.neqv.` |

Example:

```fortran
x = a + b * c**2
```

Equivalent to:

```fortran
x = a + (b * (c**2))
```

Use parentheses when in doubt.

---

## **Array Operations**

Fortran operators work directly on arrays.

```fortran
real :: a(3), b(3), c(3)

a = [1.0, 2.0, 3.0]
b = [10.0, 20.0, 30.0]

c = a + b
```

Result:

```
c = [11.0, 22.0, 33.0]
```

Operations are applied element-wise.

---

## **Array Slices in Expressions**

Array sections can be used in arithmetic:

```fortran
a(1:5) = b(1:5) + c(1:5)
```

The array shapes must be compatible.

```fortran
a(1:8) = b(2:9)
```

works because both sections contain 8 elements.

---

## **String Concatenation**

Strings can be joined using `//`.

```fortran
name = "Fred" // "die"
```

Result:

```
Freddie
```

---

## **Key Ideas**

- `=` means assignment, not mathematical equality.
- Arithmetic operators work on scalars and arrays.
- Relational operators return logical values.
- Logical operators combine conditions.
- Array operations are performed element-wise.
- Parentheses can be used to control evaluation order.
- `//` concatenates strings.