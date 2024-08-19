# Lab 1: Compiling, Assembling, and Comparing Performance

## Setup

Get yourself into a Linux environment.

- In CSIL, that might require rebooting from Windows to Linux.
- On your own Windows computer, you might need to install Windows Subsystem for Linux or another virtual machine that can run a Linux OS.
- Or with Windows, you could dual boot Linux.
- On a Mac, you will need some kind of virtual machine software to create a VM and install Linux.

Get the basic development tools installed: `gcc`, `as`, `ld`. If you're on a Debian/Ubuntu/Mint or similar Linux distribution, you can install the build-essential package to get them.

## Compiling and Assembling
Recall from lecture, when I say "gcc" what I mean is:

```
gcc -Wall -Wpedantic -std=c17 -march=haswell
```

Or possibly:

```
clang -Wall -Wpedantic -std=c17 -march=haswell
```

In the provided `lab1.zip` (link at the top of this page), there is a file `array_sum.S` that contains assembly code implementing a function _array_sum_ that sums the elements of an array of _int32_t_ values. The C signature for this function can be found in `array_sum.h`.

The provided `array_sum_speed.c` uses this function: it creates a random array, gets its sum with the array_sum function, and times it.

1. Assemble array_sum.S to produce array_sum.o.
2. Compile array_sum_speed.c to produce array_sum_speed.o.
3. Link the two .o files to produce an executable (possibly named a.out, or possibly something else).
4. Run the executable, giving a command line argument "1000000" for the array size.

Do **not** do unnecessary steps (like run the preprocessor by hand) as demonstrated in lecture. That was a demo of the tools, not a practical method to create an executable. Each of those should be a single command.

❓ [← that's a signal that you should look at the "Questions" section below, because there's something there to answer about this part.]

## Replace Assembly with C
Create an `array_sum.c` file that contains a C implementation of the `array_sum` function: it should have a signature that matches `array_sum.h` and return the sum of elements `0` to `n-1` of the array. It should return the same result as the assembly implementation in all cases.

Repeat the above, but replace the first step ("assemble") with a `gcc` or `clang` command that compiles your C implementation to create `array_sum.o`.

Compare the relative speed of the two `array_sum` implementations (using the time taken which is printed by the main function). ❓

You may get more accurate timing results if you prevent your CPU from slowing down to save power: see the Timing Code page for info.

**Compile it again, but better**

Add the `-O3` switch to the command that compiles `array_sum.c` and repeat the timing. ❓

## Replace C with Assembly
See the provided `multiply_add.c`, `multiply_add.h`, and `multiply_add_test.c` files.

The `multiply_add.c` and `multiply_add.h` describe a simple C function. Code to test it can be found in `multiply_add_test.c`.

Write an assembly replacement `multiply_add.S` that can be used in place of `multiply_add.c`. It should define a `multiply_add` function that behaves exactly the same as the C implementation.

Hint: the instructions that you need to do the integer arithmetic are `add` and `imul`. You can find the three function arguments in `%rdi`, `%rsi`, and `%rdx` (in that order). The result is returned by putting it in `%rax`.

Suggestion: first create a function that always returns 0. Test. Then modify so it always returns `c`, then `b*c`, then `a+b*c`.

## Questions
Answer these questions in a text file `answers.txt`. [Generally, these questions should be answered in a few sentences each.]

1. What were the four commands you used to assemble, compile, link, and run in the "Compiling and Assembling" section?
2. What was the first command you used to compile `array_sum.c`? How must faster/slower was your C implementation compared to the provided assembly?
3. How did the speed of the C and assembly implementations of `array_sum` compare after you added `-O3` to the compilation command? How did C speed compare with and without `-O3`?