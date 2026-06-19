# Characters

# **Character (Strings)**

## **Declaration**

Strings are declared using the `character` type.

Recommended explicit syntax:

```fortran
character(len=20) :: name
```

rather than older Fortran 77 styles:

```fortran
character*20 name
character name*20
```

---

## **Variable-Length Strings**

Fixed-length strings:

```fortran
character(len=20) :: name
```

always reserve 20 characters.

Variable-length strings:

```fortran
character(len=:), allocatable :: name
```

can be allocated dynamically and are generally preferred.

---

## **String Arrays**

```fortran
character(len=20), dimension(100) :: name_list
```

Array of 100 strings, each with length 20.

## Arrays of Variable-Length Strings

You can create arrays of strings dynamically:

```fortran

character(len=:), allocatable, dimension(:) :: names

allocate(character(len=20) :: names(100))
```

---

## **Passing Strings to Procedures**

```fortran
subroutine up(string)
    character(len=*) :: string
end subroutine
```

`len=*` means the procedure accepts strings of any length.

---

## **String Concatenation**

Use `//` to join strings:

```fortran
character(len=20) :: name

name = "Fred" // "die"
```

Result:

```
Freddie
```

---

## **Substrings**

Access part of a string using:

```fortran
string(start:end)
```

Example:

```fortran
character(len=10) :: string

string = "Alphabet"

string(5:5) = "O"
```

Result:

```
AlphObet
```

Replace everything from position 4 onward:

```fortran
string(4:) = "ine"
```

Result:

```
Alpine
```

---

## **Removing Trailing Spaces**

Fixed-length strings are padded with spaces.

```fortran
print *, trim(string)
```

`trim()` removes trailing spaces.

---

## **Quotes Inside Strings**

Use the opposite quote type:

```fortran
apostrophe = "don't"
```

or escape by doubling the quote:

```fortran
apostrophe = 'don''t'
```

---

---

## **Substrings of String Arrays**

Array indexing and substring indexing can be combined:

```fortran
character(len=3), dimension(2) :: words

words = ['ls', 'cat']

print *, words(2)(2:3)
```

Result:

```
at
```

Here:

- `words(2)` selects `"cat"`
- `(2:3)` selects characters 2–3

---

## **String Alignment**

Shift text to the left:

```fortran
string = adjustl(string)
```

Example:

```
"   hello" → "hello   "
```

This is useful when reading fixed-width text files.

---

## **Fixed-Length Strings and Padding**

For fixed-length declarations:

```fortran
character(len=20) :: name

name = "Fred"
```

Internally Fortran stores:

```
"Fred                "
```

Unused characters are filled with spaces, which is why `trim()` is commonly used.

```
These are the only really useful new concepts from the next two slides that aren't already covered in your notes.
```

---

## **String Length Inquiry**

The length of a string can be obtained using:

```fortran
len(string)
```

Example:

```fortran
character(len=20) :: name

print *, len(name)
```

Result:

```
20
```

---

## **UTF-8 / Unicode Strings**

For international characters, a character kind can be specified:

```fortran
character(
    kind=selected_char_kind('ISO_10646'),
    len=100
) :: text
```

This enables Unicode (UTF-8/UTF-16 related) character support.

## How to Build a History Log

Use an allocatable string to iteratively add information while doing work

```fortran
character(len=:), allocatable :: history

history = ""

history = history // "Loaded data; "
history = history // "Processed data; "
history = history // "Saved results; "

print *, history
```

Output:

```
Loaded data; Processed data; Saved results;
```

### **Why use an allocatable string?**

- The string can grow as new information is added.
- No need to know the final length in advance.
- Useful for logging program steps, metadata, or diagnostic messages.

### **Key Idea**

Use string concatenation (`//`) to append new information:

```fortran
history = history // "new message"
```

## **Common String Functions**

| **Function** | **Purpose** |
| --- | --- |
| `len(str)` | String length |
| `trim(str)` | Remove trailing spaces |
| `index(str, sub)` | Find substring |
| `adjustl(str)` | Left-align text |
| `adjustr(str)` | Right-align text |

### **Key Ideas**

- Strings are fixed-length by default.
- Use `character(len=:), allocatable` for flexible string lengths.
- `//` concatenates strings.
- `string(i:j)` extracts or modifies substrings.
- `trim()` removes trailing spaces.

## Useful Tools for Strings

| **Function** | **Purpose** |
| --- | --- |
| len(string) | Total length of a string |
| len_trim(string) | Length excluding trailing spaces |
| trim(string) | Remove trailing spaces |
| adjustl(string) | Shift text to the left |
| adjustr(string) | Shift text to the right |
| index(string, pattern) | Find the position of a substring |
| scan(string, set) | Find first occurrence of any character in a set |
| verify(string, set) | Find first character **not** in a set |
| char(i) | Convert integer ASCII code to character |
| iachar(c) | Convert character to ASCII code |