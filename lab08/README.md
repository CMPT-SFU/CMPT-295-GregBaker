# Lab 8
## 2D Arrays and Cache

For this question, you will complete the provided `2d-array.c.`

The provided `create_array` will create and fill a one-dimensional array of a given length.

A two-dimensional _h_ by _w_ array has exactly the same memory layout as a one-dimensional _w×h_ array. How we view the bytes is our choice. We will now pick a width w and height h where _w×h = n _and treat the array as a two-dimensional _h_ by _w_ array.

The question here is: what is the performance difference if we traverse the array, summing its contents by-row or by-column? For example, in this 2×3 array,

     1  2  3
   
     4  5  6

… the by-row sum would calculate in this order: 1+2+3+4+5+6. The by-column sum would calculate 1+4+2+5+3+6.

**Complete the sum_array_row and sum_array_col functions**. Both should calculate the same value, with the array accessed in different orders. As much as possible, keep the calculations the same so we can see the cost of memory access in isolation.
### Experiment
The provided `array-2d.c` assumes an array size on the command line. It may be wise to do a "`ulimit -v`" before you start: that will limit the virtual memory used by your shell (and processes you start from it). That could save you from some accidental zeros on an array size bringing your computer to its knees.

```
ulimit -v 4194304  # limit to 4GB virtual memory
gcc -Wall -Wpedantic -std=c17 -march=haswell -O3 array-2d.c
./a.out 5120000
```

Try some different array sizes and see how the "by-row" and "by-column" array traversals behave, both for different width/heights on the same n, and relative to each other. ❓

The `lscpu` command will report the sizes of your L1/L2/L3 caches, which will be relevant.

Array sizes that have a lot of factors are interesting: [highly composite numbers](https://gist.github.com/dario2994/fb4713f252ca86c1254d#file-list-txt), or even just powers of two followed by some zeros (like 5120000).
### One more thing...

The provided code contains "`malloc`" but not "`free`". Fix it: make sure all heap memory is freed when appropriate.

## Revisiting Hailstone
Recall the `hailstone_length` function from Lab 3. In that exercise, it was practice using conditional branches. Now let's re-implement the same funcion as `hailstone_length_cmov` using **conditional moves** and see how the speed compares.

To do this, in your loop calculate **both** `3n + 1` and `n/2` and use a conditional move to put the "right" one into the register where you want it.

You will still need one conditional branch to continue/exit the loop, but the next hailstone value should be determined with a conditional move.

Include your `hailstone_length` function from Lab 3 in `lab8.S`. A C implementation has been provided in `lab8c.c`. Some tests have been provided in `tests.c`.

Have a look at the running time (on real hardware not an emulated system; maybe a VM will be okay). The provided `timing.c` will time the execution of the various hailstone length functions with many inputs. ❓

## Questions
Answer these questions in a text file `answers.txt`.
1. How much faster was the by-row access of the rectangular array than the by-column when your array **fits in L1 cache**? (Compare the worst case of each.)
2. How does the performance of the by-column summation change as the array gets larger than the caches?
3. Did avoiding the branch with a conditional move speed up the hailstone length calculation? By how much? How did the C code compare? [I believe this is a very rare case where our assembly can slightly beat `gcc -O3`. Sadly, `clang -O3` still beats my times.]
