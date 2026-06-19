# Dynamic Data

## Contents

1. [Pointers](https://claude.ai/cowork/local_8a07cf96-7202-45f5-9aa9-41a983bfdbde#1-pointers)
2. [Dynamic Data — Four Approaches](https://claude.ai/cowork/local_8a07cf96-7202-45f5-9aa9-41a983bfdbde#2-dynamic-data--four-approaches)
    - [(1) Pointer Arrays](https://claude.ai/cowork/local_8a07cf96-7202-45f5-9aa9-41a983bfdbde#21-pointer-arrays)
    - [(2) Allocatable Arrays](https://claude.ai/cowork/local_8a07cf96-7202-45f5-9aa9-41a983bfdbde#22-allocatable-arrays-recommended)
    - [(3) Automatic Arrays](https://claude.ai/cowork/local_8a07cf96-7202-45f5-9aa9-41a983bfdbde#23-automatic-arrays)
    - [(4) Dynamic-Length Characters](https://claude.ai/cowork/local_8a07cf96-7202-45f5-9aa9-41a983bfdbde#24-dynamic-length-characters-fortran-2003)
3. [Array Types and Descriptors](https://claude.ai/cowork/local_8a07cf96-7202-45f5-9aa9-41a983bfdbde#3-array-types-and-descriptors)
4. [Arrays with Different Ranks (Fortran 2018+)](https://claude.ai/cowork/local_8a07cf96-7202-45f5-9aa9-41a983bfdbde#4-arrays-with-different-ranks-fortran-2018)
5. [Contiguous Attribute (Fortran 2008+)](https://claude.ai/cowork/local_8a07cf96-7202-45f5-9aa9-41a983bfdbde#5-contiguous-attribute-fortran-2008)
6. [Dynamic Derived Types / Parametrized Types (Fortran 2003+)](https://claude.ai/cowork/local_8a07cf96-7202-45f5-9aa9-41a983bfdbde#6-dynamic-derived-types--parametrized-types-fortran-2003)
7. [Array Syntax](https://claude.ai/cowork/local_8a07cf96-7202-45f5-9aa9-41a983bfdbde#7-array-syntax)
8. [Performance Implications](https://claude.ai/cowork/local_8a07cf96-7202-45f5-9aa9-41a983bfdbde#8-performance-implications)

---

## 1. Pointers

### Concept

- A pointer in Fortran 90 is **not a type** — it is an **attribute** on any variable.
- Pointers store a **descriptor** (base address + shape + stride), not a raw memory address.
- Any variable to be pointed at must carry the **`target`** attribute.

```fortran
integer, pointer  :: ip, iq
real,    pointer  :: rp
real,    pointer  :: p_feld(:)   ! pointer to a 1-D array

integer, target   :: my_int
ip => my_int                     ! pointer assignment
```

### Pointer Assignment

- `=>` associates a pointer with a target (or another pointer).
- Regular `=` **dereferences** the pointer and copies the value.

```fortran
ip => iq          ! ip now points to the same location as iq
ip  = 5           ! writes 5 through ip into the target
```

### Association Status

A pointer is in one of three states:

| Status | Description | How to reach |
| --- | --- | --- |
| **Associated** | Points to a valid target | `allocate(p)` or `p => target` |
| **Disassociated** | Explicitly null | `nullify(p)` or `pointer :: p => null()` |
| **Undefined** | After declaration without init | (avoid — undefined behavior) |

```fortran
integer, pointer :: p
allocate(p)           ! now associated
nullify(p)            ! now disassociated (but memory is leaked!)
deallocate(p)         ! correct way to free memory
if (associated(p)) ...
if (associated(p, target)) ...   ! checks if p points to target
```

### Memory Leaks

`nullify` without `deallocate` causes a **memory leak** — the heap memory is orphaned. Always `deallocate` before `nullify` or reassignment if the pointer owns the memory.

### Pointer Arrays

```fortran
real, pointer :: p(:)
allocate(p(10))
! resize: allocate temp, copy, move
real, pointer :: tmp(:)
allocate(tmp(20))
tmp(1:10) = p(1:10)
deallocate(p)
p => tmp
```

Pointer arrays:

- Live on the **heap**
- Are **not automatically deallocated**
- May be **non-contiguous** (e.g. after `p => arr(1:n:2)`)

### Linked Lists and Trees

Pointers enable recursive data structures:

```fortran
type list_node
  real                        :: value
  type(list_node), pointer    :: next => null()
end type

! Traversal
do while (associated(link%next))
  link => link%next
end do
```

Binary trees use two pointer components (`left`, `right`) and a recursive subroutine with `recursive` keyword.

### Procedure Pointers (Fortran 2003+)

Pointers can point to procedures, enabling runtime dispatch:

```fortran
abstract interface
  function math_func(x) result(res)
    real, intent(in) :: x
    real             :: res
  end function
end interface

procedure(math_func), pointer :: fp => null()
fp => sin
print *, fp(1.0)
```

- `nopass` — procedure pointer does not receive the object as a hidden argument.
- `pass` / `pass(arg)` — type-bound procedure pointers pass the object.

### Pointer Functions (Fortran 2008+)

A function can return a pointer, allowing the call site to write through it:

```fortran
function get_element(arr, i) result(p)
  real, target, intent(inout) :: arr(:)
  integer, intent(in)         :: i
  real, pointer               :: p
  p => arr(i)
end function
```

### Intent with Pointers

| `intent` | Pointer itself | Target |
| --- | --- | --- |
| `intent(in)` | Association cannot change | Target readable |
| `intent(out)` | Association may change | Target writable |
| `intent(inout)` | Association may change | Target readable/writable |

---

## 2. Dynamic Data — Four Approaches

### 2.1 Pointer Arrays

See [§1 Pointer Arrays](https://claude.ai/cowork/local_8a07cf96-7202-45f5-9aa9-41a983bfdbde#pointer-arrays). Summary: flexible but error-prone — prefer allocatables.

---

### 2.2 Allocatable Arrays (Recommended)

The preferred mechanism for dynamic memory in modern Fortran.

```fortran
real, dimension(:,:), allocatable :: a
allocate(a(100, 200))
! ... use a ...
deallocate(a)   ! explicit, or automatic at end of scope (Fortran 2003+)
```

**Key advantages over pointer arrays:**

- **Auto-deallocated** at end of scope (Fortran 2003+) — no memory leaks.
- Always **contiguous** in memory.
- Compiler can optimize more aggressively (no aliasing concerns).
- Can be used as `intent(out)` dummy arguments (auto-allocated by callee).
- Can be components of derived types.

### Restrictions

- `allocate(arr)` on an **already-allocated** array is a runtime error — must `deallocate` first.
- `deallocate(arr)` on a **not-allocated** array is a runtime error.
- Cannot pass an array section or array constructor literal to an `allocatable` dummy argument; use **assumed-shape** (`dimension(:)`) instead.

```fortran
! allocatable dummy:
subroutine up_a(alpha)
  real, dimension(:), allocatable :: alpha
end subroutine

! assumed-shape dummy (more flexible):
subroutine up_b(alpha)
  real, dimension(:) :: alpha
end subroutine

real, dimension(10) :: aa
call up_a(aa)         ! ERROR — aa is not allocatable
call up_b(aa)         ! OK

real, dimension(:), allocatable :: bb
allocate(bb(20))
call up_a(bb)         ! OK
call up_a(bb(1:4))    ! ERROR — array section is not allocatable
call up_b(bb(1:4))    ! OK — treated as assumed shape
```

### Generalized Allocation: Typed vs. Sourced

```fortran
! Typed allocation (standard form):
allocate([type-spec ::] alloc-list [, stat=stat])

! Sourced allocation — copies shape AND values from source:
real, dimension(:) :: arr1, arr2
allocate(arr2, source=arr1)   ! arr2 gets same shape and values as arr1

! Useful for polymorphic variables:
type(my_type), dimension(:), allocatable :: arr
class(*), allocatable, dimension(:)      :: flexible
allocate(flexible, source=arr)
```

Typed and sourced allocation are **mutually exclusive**.

### `move_alloc` (Fortran 2003+)

Transfers the descriptor (not the data buffer) from one allocatable to another. Fast for large arrays — no copy.

```fortran
call move_alloc(from, to)
```

- `to` gets the allocation status of `from`.
- `from` becomes unallocated after the call.
- If `to` was already allocated, it is deallocated first.

**Use case — cheap array swap during time-stepping:**

```fortran
real(kind=rk), allocatable, dimension(:) :: ar_current, ar_old, help
! ... initialize ...
do jj = 1, jjmax
  call time_step(ar_current, ar_old)
  call move_alloc(ar_current, ar_old)   ! rename ar_current → ar_old
end do
```

### Enlarging / Resizing Allocatable Arrays (Fortran 2003+)

Assignment from an array constructor auto-resizes the left-hand side:

```fortran
real(kind=rk), allocatable, dimension(:) :: values
values = [1._rk, 5._rk]              ! size 2
values = [values, 6._rk, 2._rk, 3._rk]  ! size 5 (auto-resize)
```

Using `move_alloc` for explicit resize (avoids deep copy):

```fortran
subroutine resize_alloc(alloc, new_size)
  type(alloc_type) :: alloc
  integer          :: new_size
  real(kind=rk), allocatable, dimension(:) :: temp
  if (new_size > size(alloc%data)) then
    allocate(temp(new_size))
    temp(1:alloc%number_of_data) = alloc%data(1:alloc%number_of_data)
    call move_alloc(temp, alloc%data)   ! no deallocation of alloc%data needed
  end if
end subroutine
```

### Recursive Allocatable Components (Fortran 2008)

```fortran
type entry
  real    :: value
  integer :: index
  type(entry), allocatable :: next   ! linked list via allocatable
end type
```

> ⚠️ As of gfortran 9 / ifort 19, recursive allocatable components may not work as expected. Use `move_alloc`-based workaround.
> 

---

### 2.3 Automatic Arrays

Automatic arrays are local variables with variable-length dimensions defined at procedure entry:

```fortran
subroutine auto_array(imax, dummy_array)
  integer,            intent(in) :: imax
  integer, dimension(:)          :: dummy_array
  real, dimension(imax)          :: local_array1  ! automatic — destroyed on return
  real, dimension(size(dummy_array)) :: local_array2  ! automatic
end subroutine
```

- Created on the **stack** when the procedure is entered.
- Destroyed **automatically** on return (no deallocation needed).
- Stack size must be large enough (can be a problem for large arrays).
- May be faster than heap-allocated arrays for small sizes.
- Have lost relevance with the comprehensive features of allocatables.

---

### 2.4 Dynamic-Length Characters (Fortran 2003+)

Character strings can have dynamically adjusted length using the **`allocatable`** attribute:

```fortran
character(len=:), allocatable :: string
string = 'simple'         ! len = 6
string = 'short'          ! len = 5 (auto-resize)
string = string // ' sentence'  ! len = 14 (concatenation)
string(:) = ''            ! len still 14 — blanks it, does not resize
```

- Declared with `len=:` (deferred length).
- Can be a component of a derived type.
- Does **not** work for character arrays (only scalars and derived type components).
- Length can potentially be GigaBytes depending on the compiler.

**Deferred-length string in a derived type (enabling variable-length array of strings):**

```fortran
module alloc_strings_module
  type dyn_string_type
    character(len=:), allocatable :: string
  end type
end module

type(dyn_string_type), dimension(10) :: dyn
dyn(4)%string = 'simple'   ! len = 6
```

---

## 3. Array Types and Descriptors

### Array Shape Kinds

| Kind | Declaration | Notes |
| --- | --- | --- |
| **Explicit-shape** | `dimension(nmax)` | Conventional Fortran 77 |
| **Assumed-size** | `dimension(nmax, *)` | Last dim unknown; `size()` not applicable |
| **Assumed-shape** | `dimension(:)` | Preferred dummy argument form |
| **Allocatable** | `dimension(:), allocatable` | Deferred-shape, heap |
| **Pointer** | `dimension(:), pointer` | Deferred-shape, heap, may be non-contiguous |
| **Automatic** | `dimension(size(other))` | Local, stack |
| **Implied-shape (parameter)** | `integer, parameter :: order(0:*) = [0,1,2,3]` | Named constant, size from RHS |

### Array Descriptors

- **Explicit-shape and assumed-size** arrays do **not** use a descriptor — just a base pointer.
- **Allocatable arrays** and **pointer arrays** use a **descriptor** containing base address, type, rank, and per-dimension stride/bounds.
- `move_alloc` transfers the descriptor, not the data.

### AESA — Array Element Sequence Association

Fortran multidimensional arrays use **column-major** (first-index varies fastest) storage, identical across all Fortran compilers:

```
a(j, k, l)  with  real :: a(jx, kx, lx)
→ memory element at a(1,1,1) + (j-1) + jx*(k-1) + jx*kx*(l-1)
```

- Explicit-shape arrays passed to assumed-size subroutines exploit AESA to change rank.
- Arrays in C/C++/Java are arrays-of-arrays; Fortran multidimensional arrays are a flat contiguous block.

### Declaration of Array Dummy Parameters (summary)

```fortran
subroutine up(n, p, r, s, u, v, bb)
  integer                        :: n
  real, dimension(1:15, 5, 10)   :: p      ! explicit-shape
  real                           :: r(1:15, n, *)  ! assumed-size
  real, dimension(:, 5:)         :: s      ! assumed-shape
  real, allocatable, dimension(:,:) :: u   ! dynamic (allocatable)
  real, pointer, dimension(:)    :: v      ! array pointer
  real, dimension(size(s,2), n)  :: bb     ! automatic (local)
end subroutine
```

Default lower bound for assumed-shape and pointer arrays is **1**.

---

## 4. Arrays with Different Ranks (Fortran 2018+)

### Assumed-Rank Arrays

Declared with `dimension(..)` — accepts any rank at the call site:

```fortran
subroutine print_bounds(array)
  real(kind=dk), dimension(..), allocatable :: array
  integer :: ll, ll_max
  ll_max = rank(array)   ! intrinsic: number of dimensions
  do ll = 1, ll_max
    write(*, *) lbound(array, ll), ':', ubound(array, ll)
  end do
end subroutine
```

- Requires an explicit interface.
- Cannot be an element of a derived type.
- Syntax: `dimension(..)` — no spaces inside `(..)`.

### `select rank` Construct (Fortran 2018+)

Dispatch on the actual rank at runtime:

```fortran
select rank(array)
rank(0)
  ! scalar
rank(1)
  ! 1-D — array is treated as declared dimension(lbound:ubound)
rank(2)
  ! 2-D
rank(*)
  ! assumed-size
rank default
  ! any other rank not explicitly listed
end select
```

- Within each block the array is treated as if declared with that rank.
- Bounds obtained via `lbound` and `ubound`.
- Can be named: `my_select: select rank(array)`.

```fortran
! Example — rank-generic write subroutine
subroutine write_array(array, unit)
  real(kind=dk), dimension(..), allocatable :: array
  integer :: unit, ll
  select rank(array)
  rank(1)
    do ll = 1, size(array, 1)
      write(unit, '(e15.5)') array(ll)
    end do
  rank(2)
    do ll = 1, size(array, 1)
      write(unit, '(*(e15.5))') array(ll, :)
    end do
  end select
end subroutine
```

> Available in gfortran 11+. **`END SELECT`** not `END SELECT RANK`.
> 

### Integer Arrays for Rank and Bounds (Fortran 2023+)

Ranks and extents can be specified by integer arrays:

```fortran
integer :: lb_array(3), ub_array(3)
real(kind=dk), dimension(lb_array:ub_array) :: alpha

! Shape of one array defines shape of another:
real(kind=dk), dimension(shape(array)) :: other_array

! Rank as attribute:
real(kind=dk), rank(2) :: array   ! declares a rank-2 array
```

> Not yet working in gfortran 17.
> 

---

## 5. Contiguous Attribute (Fortran 2008+)

Specifies that array elements are contiguous in memory (stride 1). Enables better compiler optimization.

```fortran
real, pointer,     contiguous :: contiguous_pointer(:)
real, contiguous, dimension(:,:) :: assumed_shape_2D
```

- **Allocatable arrays are always contiguous** — no need for the attribute.
- A **pointer with `contiguous`** may only be associated with a contiguous target.
- An **assumed-shape dummy with `contiguous`**: if a non-contiguous actual argument is passed, the compiler creates a contiguous temporary copy on entry and copies back on exit.
- `IS_CONTIGUOUS(array)` — intrinsic function returning `.true.`/`.false.`

**When is an array section contiguous?**

```fortran
aa(10:20, 3)      ! contiguous — single column
bb(:, i:j, 2)     ! contiguous by AESA
dd(jk, :)         ! NOT contiguous — jk is a scalar → stride across rows
```

**Performance impact:** Non-contiguous pointer arrays force the compiler to generate code for the general stride case, which is slower. Adding `contiguous` to an assumed-shape dummy lets the compiler use stride-1 code paths.

---

## 6. Dynamic Derived Types / Parametrized Types (Fortran 2003+)

### Parametrized Derived Types (PDT)

Type parameters are declared with `kind` (compile-time) or `len` (runtime) attributes:

```fortran
type :: node_set_type(kd, ln)
  integer, kind :: kd          ! kind parameter — like a type parameter
  integer, len  :: ln = 0      ! len parameter — can vary at runtime
  real(kind=kd), dimension(ln) :: x, y, z
end type node_set_type

! Usage:
type(node_set_type(kd=dk, ln=nmax)), allocatable :: nodes
allocate(nodes_type(dk, ln=nmax) :: nodes)
```

- `kind` parameters are constants (like kind selectors).
- `len` parameters can vary — similar to allocatable components but built into the type.

---

## 7. Array Syntax

Fortran treats arrays as first-class data objects — operations apply element-wise.

### Whole-Array Operations

```fortran
real, dimension(5, 5) :: A, B, C
A = 3.2              ! fill all elements
B = A * 1.5          ! element-wise multiply
C = A * B            ! element-wise (NOT matrix multiplication!)
```

### Array Sections

```fortran
real, dimension(2,3) :: array_a
real, dimension(3,2) :: array_b
real, dimension(3)   :: vector
array_a(1, :) = array_b(:, 2) - 1.2 * vector
```

### Array Constructors

Rank-one arrays; use `reshape` for higher rank:

```fortran
AA = [B(l,1), B(l+1,1), c(2:6, 1:4)]   ! first columns, then rows
BB = (/1, 3, 5, 7, 9/)                  ! old syntax
CC = [(2*i-1, i=1, 5)]                  ! implied do-loop constructor
```

Since Fortran 2008 — typed constructors:

```fortran
commands     = [character(len=8) :: "ls", "cat", "more"]
string_array = [character(len=17) :: string1, string2, '12345']
```

### `WHERE` — Masked Array Assignment

```fortran
real, dimension(10,10) :: A, recip_A
where (A /= 0.0)
  recip_A = 1.0 / A
elsewhere               ! Fortran 95
  recip_A = 0.0
end where
```

`WHERE` is element-wise masking — replaces conditional loops cleanly.

---

## 8. Performance Implications

> Key principle: **flexibility and performance are often contradictory**. Design your data layout for the most critical loops.
> 

### General Rules

- **Use arrays** — avoid pointers for data that lives in hot loops.
- **Avoid pointers** — pointer arrays force the compiler to assume potential non-contiguity (aliasing), preventing vectorisation.
- **Iterate on the first (innermost) dimension** — Fortran is column-major; accessing `a(i, j, k)` in a loop over `i` is cache-friendly.
- **Prefer allocatable over pointer** — allocatables are always contiguous and allow alias-free optimisation.
- **Prefer SoA over AoS** — use arrays of scalars as derived type components (Structure of Arrays) rather than arrays of derived types (Array of Structures); avoids strided access to fields.
- **Never use linked lists for time-critical loops** — pointer chasing kills cache performance by orders of magnitude.

### Array Attribute Performance Ranking (daxpy benchmark)

From highest to lowest performance (GCC 11–12):

1. `allocatable, dimension(:)` — compiler knows memory is contiguous
2. `contiguous, dimension(:)` — explicit contiguity hint for assumed-shape
3. `dimension(:)` (assumed-shape) — compiler assumes possible non-contiguity
4. `pointer, dimension(:)` — worst; most conservative code generated

> ⚠️ Results vary across compiler versions (GCC 11 vs 12 vs 17 show different orderings) — benchmark on your target compiler.
> 

### Loop Unrolling

- Compiler typically unrolls at `O2`/`O3`.
- Manual unrolling by 2×/4×/8× can improve cache use for small arrays but hurts for arrays larger than cache.

### Dimension Access Patterns

- Iterating on the **first** dimension is cache-friendly.
- Adding extra unit-extent dimensions generally degrades performance at small loop lengths; effect diminishes for large arrays (compiler can use wider SIMD).
- Iterating on a **non-first** dimension is counterproductive — restructure loops.

### Structure of Arrays vs Array of Structures

```fortran
! AoS (Array of Structures) — BAD for hot loops accessing one field
type t_rxy
  real(8) :: r, x, y
end type
type(t_rxy), dimension(:) :: d   ! d(i)%x strided by sizeof(t_rxy)

! SoA (Structure of Arrays) — GOOD
type t_rxyArr
  real(8)                          :: r, x, y
  real(8), dimension(:), allocatable :: array
end type
! or simply separate arrays:
real(8), dimension(:) :: r, x, y
```

### Key Takeaways

- Performance differences are primarily due to **data layout** and **memory access patterns**.
- These patterns are not Fortran-specific — the same applies to C, C++, etc.
- Simplify data access for frequently used data; cache hierarchy dominates for large arrays.
- Avoid hidden access via procedure calls for frequently used data.

---

*Notes compiled from HLRS lecture slides "Fortran dynamic data" (140 slides, Part 3), HLRS Stuttgart.*