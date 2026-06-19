# Basics 2 - Starting Exercises

### Overview

```bash
Source code (.f90)
        ↓
     Compile
        ↓
Executable
        ↓
      Run
```

### Converting Windows files to Linux

```fortran
cd $MYSCR → workspace home
cd Fortran_Exercises_and_Tests
dos2unix all_dos2unix.sh
. all_dos2unix.sh
```

### Running a File

```bash
gfortran my_file.f90
# This creates an out file e.g. a.out

# Now run the output
./a.out
```

**In most of the exercises we have the [compile.sh](http://compile.sh) file**

which looks like:

```bash
gfortran Hello_World_with_errors.f90 -o Hello_World_with_errors.exe
```

### **Compiling with `gfortran`**

Compile a source file and create an executable:

```bash
gfortran program.f90 -o myexec
./myexec
```

Useful flags:

- `g` : include debugging information
- `fcheck=all` : enable runtime checks
- `fbounds-check` : check array bounds

Optimization levels:

- `O0` : no optimization (best for debugging)
- `O1`, `O2`, `O3` : increasing optimization
- `Ofast` : maximum speed, may relax some standards

For larger programs, source files are often compiled separately into **object files** (`.o`) using `-c`, then linked together into a single executable:

```bash
gfortran -c -O3 p1.f90
gfortran -c -O3 p2.f90
gfortran -c -O3 main.f90
gfortran p1.o p2.o main.o -o prog.out
```

Workflow:

```
.f90 source files → .o object files → executable
```

This allows large projects to be compiled more efficiently, since only modified source files need to be recompiled.

### Requesting Interactive Node

*Intended to be used to executing files*

*Keep a login node open as well for compiling*

```bash
srun --pty \
  --nodes=1 \
  --cpus-per-task=1 \
  --mem=16G \
  --time=03:00:00 \
  --partition=smp \
  bash
```

### Batch Script to Sbatch the Executable

```bash
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --time=00:10:00
#SBATCH --partition=work

./my_executable
```