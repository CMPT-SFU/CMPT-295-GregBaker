# Lab Exercise 5

## More Recursion
For more recursion practice, we'll write an assembly function to generate Fibonacci numbers using the very bad recursive [algorithm](https://bosker.wordpress.com/2011/04/29/the-worst-algorithm-in-the-world/):

```Python
def fibonacci(n):
    if n <= 1:
        return n
    else:
        return fibonacci(n-1) + fibonacci(n-2)
```
Some tests are included in the `timing.c` program provided for the text part.

## Timing Alternate Implementations
In these sections, we will make some comparisons between functions that calculate the same results in different ways. In each case, you'll write an implementation #1 and #2 where we expect #2 to be faster. A #3 C implementation of each has been provided in `others.c`.

**The timing will be subtle** so it will probably be necessary to look at the results on real hardware: remote in to a CSIL workstation if necessary. There may also be differences between processors, so the expected results may differ from what you see.

### The Setup
The provided `timing.c` will import your functions and test them with the `CODE_TEST` macro. (You can comment-out `CODE_TEST(…)` lines while you're working and don't have all of the functions implemented.)

The provided code does several "warmup" tests, in the hopes kicking CPUs out of a power-saving slow state. Then it takes the running time of the last "warmup" round and uses that as an estimate of the time needed to just run the test: call all of the functions and count the results. We don't want to count that time, so it's subtracted off subsequent running times so we can measure "our" work.

Also, the tests print the (sum of) the function's results. They should be the same for the comparable implementations.

You may want to adjust the `REPS` constant, depending on the speed of your processor (and patience). Don't go too low: we want tests long enough to see meaningful differences. Also don't go too long (especially on a laptop): if your CPU gets too hot, it will thermal throttle and slow down.

These commands should compile/assemble, link, and run everything. (I'm suggesting generating the implementations' object code in a separate command here, just to make sure the compiler can't optimize anything between the files, e.g. inlining the `others.c` implementations.)

```
gcc -Wall -Wpedantic -std=c17 -march=haswell -O3 -c others.c lab5.S \
&& gcc -Wall -Wpedantic -std=c17 -march=haswell -O3 others.o lab5.o timing.c \
&& ./a.out
```
### Lesson 1: Instructions Take Time
Write assembly functions `is_odd1` and `is_odd2` that takes one signed integer argument, and returns 0 if it's even and 1 if it's odd.

For `is_odd1`, use the `idiv` instruction to divide by two, and find the remainder in `%rdx`.

For `is_odd2`, use the observation that doing bitwise-AND with one will return 1 for odd numbers and 0 for even.

You will likely have a few differences (more `mov` to get the operands arranged), but the timing should be dominated by the difference between the division instruction and bitwise-AND. How much faster is it? ❓

### Lesson 2: Not All Instructions Take The Same Time
Write assembly functions `is_odd1` and `is_odd2` that takes one signed integer argument, and returns 0 if it's even and 1 if it's odd.

For `is_odd1`, use the `idiv` instruction to divide by two, and find the remainder in `%rdx`.

For `is_odd2`, use the observation that doing bitwise-AND with one will return 1 for odd numbers and 0 for even.

You will likely have a few differences (more `mov` to get the operands arranged), but the timing should be dominated by the difference between the division instruction and bitwise-AND. How much faster is it? ❓

### Lesson 3: It's Complicated
For this part, we'll write functions `mul1` and `mul2` that take an unsigned integer as their argument and return it times 65537.

For `mul1`, use the `mul` instruction to do the multiplication.

For `mul2`, use the fact that 65537 = 2<sup>16</sup> + 1 and only `shl` and `add` to calculate the value.

The conventional wisdom is that multiplying should be much slower than shifting and adding, and this should still be true in general. Is it in this (very simple) case? ❓

The answer here seems to depend on the age of your processor. You may want to compare a CSIL workstation (which have fairly new processors) with one of the CSIL CPU servers which are old Xeon CPUs (and you may have to strip `-march=haswell` from your command line on them). You can run "`lscp`u" to check the processor you have in a Linux environment.

You might also try (on your computer or others) using `-march=native` (instead of `haswell`). That gives the compiler permission to optimize for the exact processor you have, not some similar-but-different Haswell architecture.

## Questions
Answer these questions in a text file `answers.txt`. [Generally, these questions should be answered in a few sentences each.]

1. What was your test setup (i.e. what CPU model/generation did you test with? Desktop or laptop or other?)
2. What were your results: how much faster (likely expressed as a fraction/percent) is each #2 implementation? (There is no exact answer here: results will vary by run, but give your best estimate of the "truth".)
3. In each case, the C implementation (#3) was described with the "slow" algorithm. How did they compare to the "fast" algorithm after the optimizer processed them?
4. [optional] Do you see any pattern to when `mul1` is faster/slower than `mul2` on different processors/computers/whatever? Feel free to share timing results (but not solutions) with others in the course if it helps form a pattern.

