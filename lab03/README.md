# Lab 3: GDB and C
Last week, you saw using GDB (specifically the `gdbtui` interface) to inspect running assembly code. This week, we will repeat the process with C code. The included `traceable.c` program contains a few interesting functions you can examine.

You may want to look at a video introducing the basics of GDB and assembly or the brief GDB Intro.

As with the assembly code last week, **before you compile the code, add a comment** around the `factorial` and `to_base` functions with your name and student number. Then, compile and include the `-g` option on the command line to include debugging info in the object/executable file.

```
gcc -Wall -Wpedantic -std=c17 -march=haswell -g traceable.c
```

Then, start the executable in `gdbtui` and trace the execution of one or both of the functions. Some things that might be interesting to see:
- `backtrace` when `factorial` is deep in its recursion
- `watch c` or `watch v` in the `to_base` function to see how the string representation is built.
- `print (char*)pos` in `to_base` to see the string representation (partially) built.

Take a screenshot showing your `gdbtui` session with…

- the comment with your name visible in the top half of the `gdbtui` window,
- the code paused mid-execution (the current line should be highlighted),
- a breakpoint set (`b+` or `B+` beside a line of code),
- one of the above "interesting" things displayed in the lower pane of the window.

Crop the screenshot within reason and submit it.

## Conditional Branching in Assembly
For this week's lab, you will write several assembly function in `lab3.S` to get some practice with conditional branching (and status flags).

A provided `lab3.h` provides headers for these functions, and details of the argument/return types. The provided `tests.c` provides test cases for each of these functions. This code should compile, assemble, link, and run the code:

```
gcc -Wall -Wpedantic -std=c17 -march=haswell -c helpers.c tests.c \
&& as --warn -g lab3.S -o lab3.o \
&& gcc lab3.o helpers.o tests.o \
&& ./a.out
```

### Absolute Value

Write a function`abs_value` that takes one (signed integer) argument and (using a conditional branch) returns the absolute value of the argument: the argument unchanged if it's positive and negated if it's negative.

There is an instruction `neg` that takes one integer operand and negates it.

### Powers of Two

Write a function`power2` that takes one (unsigned integer) argument (`n`) and prints (using `print_uint64` from `helpers.c`, included in the ZIP file) each the powers of 2 from 1 to 2^n. You can double a number by adding it to itself: no need to multiply here.

For example, if the argument is 2, the output should be:

1

2

4

### Hailstone Sequence Length

You may have seen the hailstone sequence before (or heard of the  [Collatz Conjecture](https://en.wikipedia.org/wiki/Collatz_conjecture). The idea is to take an integer n and if it's even, the next value is n/2; if it's odd, the next value is 3n+1. It seems like iterating this calculation eventually hits 1 for all starting points.

Write a function `hailstone_length` that takes one (unsigned) integer argument, and returns the number of steps before the sequence hits one from that starting point. Here is some pseudocode for the operation (which is also Python code):

```python
def hailstone_length(n):
    length = 0
    while n != 1:
        length += 1
        if (n & 1) == 0:  # if n is even
            n = n // 2    # // is Python integer division
        else:
            n = 3*n + 1
    return length
```

Hints:

- Use the imul instruction to multiply by 3: `imul $3`, `%rxx`.
- To do the division by two, you don't have to actually divide. This instruction will divide `%rdi` by two, rounding down for odd numbers: "`shr $1, %rdi`". (If that's mysterious: we'll talk about integer representation soon.)
- You don't have to divide to decide if a number is even: doing a bitwise-AND with one will give 0 for an even number and 1 for an odd number. The `and` instruction actually does a bitwise-AND of its two arguments, so `and $1, %rdi` will set ZF for even numbers, so you can then do `jz` or `jnz` to check for even-ness. (Again, this relies on the way integers are represented with bits, which we'll talk about later.)

