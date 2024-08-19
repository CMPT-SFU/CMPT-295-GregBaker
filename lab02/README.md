# Lab 2: The GNU Debugger
For this part of the lab, you'll start working with the GNU Debugger, GDB. This is another piece of the standard toolchain, and should be present on most Linux systems. (If you have it in a Debian/Ubuntu-like distribution, it will install along with the `build-essential` package.)

You may want to look at a [video introducing the basics of GDB and assembly](https://www.youtube.com/watch?v=6f1eKXnBSDc) or a brief GDB Intro.

## GDB and Assembly
See the included `unknown.S`. It contains a function `do_something` that does _something_. Your task to to determine what it does **with the debugger** not by inspecting the code. Please resist the urge to hand-trace the code: that's not the point.

**Before you assemble the code, add a comment** around the `do_something` label with your name and student number. Then, assemble and include the `-g` option on the command line to include debugging info in the object file.

This code also uses `helpers.c`, a copy of which is included in the ZIP file. This chain of commands will compile, assemble, and link the relevant code:

```
gcc -Wall -Wpedantic -std=c17 -march=haswell -c helpers.c \
&& as --warn -g unknown.S -o unknown.o \
&& ld helpers.o unknown.o
```

[Notes: the `-g` switch isn't given on the compilation command because we don't want to look at it in GDB. We can use `ld` to link instead of `gcc` here because `helpers.c` is designed to avoid any C standard library or other C-specific linking stuff.]

Then, start the executable in `gdbtui` and trace the execution, watching to see what it calculates. In particular, the code uses the `%rbx` and `%rcx`. Watch how they change as the program runs.

You may change the argument to `do_something` (which is 60 in the provided code) if you'd like to explore the function with other inputs (but once again: this isn't a question about looking at the output, but using GDB).

Take a screenshot (the PrtScn button will probably take a screenshot in a Linux-like GUI environment) showing your `gdbtui` session with…

    the comment with your name visible in the top half of the `gdbtui` window,
    the code paused mid-execution (the current line should be highlighted),
    a breakpoint set (b+ or B+ beside a line of code),
    some relevant value `printed` or `watched` in the bottom half of the window.

Crop the screenshot within reason and submit it.

What does this function return? ❓

### Modify It
In your description of what `do_something` does, you probably said it returns a particular value "…greater than or equal to…". What if we wanted to modify the function so the result was "…strictly greater than…" instead?

Modify the program to change the logic so it doesn't return the "equal to" case, but the next higher possible value. There are at least two approaches to this: either is acceptable. [Sorry for the weird wording in this part: I'm trying to ask it without giving away the previous part.]

## Preserving Registers
In this question, we'll explore the x86-64 + Linux calling convention. You may want to refer to our [x86-64 Cheat Sheet](https://ggbaker.ca/295/x86.html) for which registers are preserved across function calls.

### The Setup

In the provided code, you have been given `f.S` which contains a two argument function that is particularly aggressive with respect to the calling convention (it deestroys the contents of all non-preserved registers). You must use it as provided.

At the other end of the program, you have been given `main.S`. It must also be used unchanged.

In between those two, you need to write `calculate.S`, which implements a one-argument function `calculate` which is described below.

When `main.S` runs, it prints 4 lines:

1. The result of a calculation on the call-preserved registers.
2. The result of `calculate(12)`.
3. The result of `calculate(-10)`.
4. The result of the same calculation on the call-preserved registers (which must be identical to the first line).

This chain of commands should assemble/compile, link, and run the code:

```
as --warn -g f.S -o f.o \
&& as --warn -g calculate.S -o calculate.o \
&& as --warn -g main.S -o main.o \
&& gcc -Wall -Wpedantic -std=c17 -march=haswell -c helpers.c -o helpers.o \
&& ld f.o calculate.o main.o helpers.o \
&& ./a.out
```

Or you can let the C compiler do a little more of the work. The result should be the same.

```
gcc -Wall -Wpedantic -std=c17 -march=haswell -g -c f.S calculate.S main.S \
&& gcc -Wall -Wpedantic -std=c17 -march=haswell -c helpers.c -o helpers.o \
&& gcc -nostdlib f.o calculate.o main.o helpers.o \
&& ./a.out
```

### Your Function
The `calculate` function that you write (in a `calculate.S`) will work with the provided `f` to `calculate` its results. Here's some pseudocode (which happens to also be Python code) that describes what the calculate function needs to do:

```python
def f(x, y):  # matches provided function f
    return x*x + y

def calculate(x):
    a = x + 3
    b = f(x, a)
    c = f(b, x)
    d = f(x, c)
    return a + b + c + d

print(calculate(12))
print(calculate(-10))
```

Write an assembly implementation of `calculate` in a file `calculate.S`. You must:

- Use **at least one call-preserved** register.
- Restore any call-preserved registers to their original values before returning (as always in an assembly function).
- Use **at least one non-preserved** register (for something other than function arguments/return). ❓
- Use `f` unchanged (in `f.S`) as it's used in the calculation above.
- Leave a comment indicating which register is used for which partial calculation (for both you and the marker).

The provided `main.S` puts some values in all of the call-preserved registers, and expects them to be unchanged (or restored) by your function. The identical calculation before and after calling your function must return/print the same result. The details of `main.S` will be changed when marking.

## Questions

Answer these questions in a text file `answers.txt`. [Generally, these questions should be answered in a few sentences each.]

1. In a few words, describe the value returned by provided `do_something` function. (This should be a natural sentence that describes the result, not a mechanical description of what the code does, and not "64" because that's what's returned for a specific argument: you should describe what the function returns in general).
2. In your `calculate` function, why did you choose a preserved/non-preserved register for the different partial results?
