# Lab 9
## Check your work: Valgrind and memory leaks
Last week, you were asked to insert `free` where appropriate to free all heap allocations in `array-2d.c`. Did you?

Try compiling your `array-2d.c` and running it under Valgrind:
```
gcc … array-2d.c
valgrind ./a.out 10000
```
Look for a "Leak Summary" or "All heap blocks were freed -- no leaks are possible".

Fix your `array-2d.c` so you are actually freeing all of the memory allocated on the heap. ❓

## Bubble Sort and Branch Prediction
In `sort.cpp`, implement a function `bubble_sort` with the given signature. The logic should be a straightforward bubble sort, analogous to this Python code:

```Python
def bubble_sort(array, length):
    for i in range(length-1):        # i from 0 to length-2
        for j in range(i+1, length): # j from i+1 to length-1
            if array[i] > array[j]:
                # swap array[i] and array[j]
                tmp = array[j]
                array[j] = array[i]
                array[i] = tmp
```
Note: it's common in bubble sort to break out of the outer loop if there were no swaps made in the inner loop. That's more efficient, but if we wanted efficiency, we wouldn't be using bubble sort. We want consistent behaviour. (i.e. this inner loop runs `n*(n-1)/2` times no matter what.)

### Examining running time

Hypotheses around the conditional branch implementing the "`if array[i] > array[j]`":

- for an already-sorted array, the branch should be very predictable: **never** do the `if` body.
- for a reverse-sorted array it should be predictable: **always** do the `if` body and swap the values.
- for a randomly-sorted array, the branch should be **unpredictable** and the `if` body runs about **half the time**.

We would like to as best possible separate the cost of bubble sorting a random array into (1) the looping and comparing, (2) running the `if` body, (3) the branch mispredictions. ❓

If those hypotheses are true, we expect time (1) to be the running time on the already-sorted array. From there, you can infer the time taken by the other aspects of the code. We will check our hypotheses below.

The code as distributed will do some timing of those scenarios. Comparison with [C's qsort](https://en.cppreference.com/w/c/algorithm/qsort) and [C++'s std::sort](https://en.cppreference.com/w/cpp/algorithm/sort) are included just to remind us how different sorting algorithms/implementations actually behave. (That's why this is a `.cpp` file not `.c`.)

An array size is expected on the command line:
```
g++ -Wall -Wpedantic -std=c++17 -march=haswell -O3 sort.cpp \
&& ./a.out 25000
```
### Did things get weird?
We have seen some students where a combination of the details of their bubble sort implementation (but not if tiny changes are made) **and** `GCC` (not `Clang`) **and ** `-O3` (not `-O2`) give very weird timing results. The problem seems to be some mis-applied compiler optimization in some very specific case.

If what you're seeing makes no sense, try switching to `-O2` at the command line: if everything is sane, there should be no difference between `-O2` and `-O3` on this code.

### Is it really branch mispredictions?

Our hypothesis was that this algorithm is slower on random arrays because of branch mispredictions. Is it really?

**Edit the main in sort.cpp** so you're doing as little work as possible, so you can mostly measure the part you care about. (The provided `just_sort` function will probably help. Note that the warmup isn't relevant to these tests so shouldn't be included.) Run the program under Perf in some different scenarios and confirm that branch predictions are/aren't happening where you expect.

See the Running Perf page for information on where/how you can run the command.

Submit your `sort.cpp` with one of these tests in the `main` function.

Compile with debugging info (`-g`) and at a lower optimization level (`-O1`) so Perf's output is as sane as possible.

**Use Perf to confirm** that the branch implied by the "if" is being frequently mispredicted on random arrays, and rarely mispredicted on already-sorted arrays. Some combination of these commands will probably be useful, but vary as you like.

```
g++ -Wall -Wpedantic -std=c++17 -march=haswell -O1 -g sort.cpp
perf stat -e branch-misses,branches,br_inst_retired.not_taken ./a.out 20000
perf record -e branch-misses:pp,branches:pp,br_inst_retired.not_taken:pp ./a.out 20000
perf report
perf annotate
```

You may need to remove the "`:pp`" from the `perf` command if it's not supported on your system. If you do, you just have to accept that the counts are only close to where they really occured when the code is annotated, but may not be on the exact instruction. (See ["EVENT MODIFIERS"](https://github.com/torvalds/linux/blob/master/tools/perf/Documentation/perf-list.txt) in the perf list docs for details if you are interested.)

Note that in Perf's code annotation… (1) The C code is almost-unreadably interspersed with the generated assembly: compiling `-O1` will make that slightly more readable, but it can still be a challenge. (2) the lines of code aren't even in "correct" order because of both reorganization by the compiler and out-of-order execution [citation needed]. (3) The "percent" that it shows is the percent of the event that it saw on that line: e.g. a "10%" indicates that 10% of the observed branch mispredictions occurred there and the other 90% were on other lines of code, not that 10% of those branches were mispredicted.

Some keys that might be useful in Perf's report: up/down arrows to move around the menus, "enter" to select a menu item, "a" to annotate a file, "esc" to go up/exit.

Some keys that might be useful in the code annotation display: up/down arrows to move through the code, "t" to toggle percent/period total count/samples, "h" for key help.

Try to confirm our hypotheses about the conditional branch in bubble sort. ❓

## 2D Arrays and Memory Locality
We imagined last week that `array-2d.c` got faster/slower because of how the memory accesses did/didn't hit cache. Were we right?

The provided `2d-main.c` contains an alternate `main` function for this program that just does a single by-column array sum so we can examine the performance of a single call to that function. It expects an array size and height on the command line, so these would be comparable runs with (presumably) different cache performance:

```
./a.out 80000000 1
./a.out 80000000 10000
```

### With Perf

As before, compile with debugging symbols and a lower level of compiler optimization. Then as before, some combination of these commands (and similar with a different height) will probably be useful. ❓
```
gcc -Wall -Wpedantic -std=c17 -march=haswell -O1 -g array-2d.c
perf stat -e L1-dcache-load-misses,L1-dcache-loads,LLC-load-misses,LLC-loads ./a.out 80000000 1
perf record -e L1-dcache-load-misses:pp,L1-dcache-loads:pp,LLC-load-misses:pp,LLC-loads:pp ./a.out 80000000 1
perf report
perf annotate
```
### With Cachegrind

Valgrind's Cachegrind tool should be able to tell us a similar story, but where Perf is sampling and monitoring, Valgrind is directly measuring and should be able to give exact numbers.

Since Valgrind comes with a performance penalty, you'll probably want to try a smaller array size. (There's one less zero in this example command.)
```
rm cachegrind.out.*
valgrind --tool=cachegrind ./a.out 8000000 1
cg_annotate cachegrind.out.*
```
The `cg_annotate` output is unpleasantly wide, but widen your terminal and/or decrease its font size and look for the line in `sum_array_col` where you do the memory access. ❓

Some of the abbreviations used by Cachegrind:

- "D refs", "Dr": data cache reads == memory reads.
- "D1 misses", "D1mr": L1 data cache read misses.
- "LLd miss rate", "DLmr": last-level data cache (L3) read misses.

## Questions

Answer these questions in a text file `answers.txt`.
1. Do you promise you got a "All heap blocks were freed" on your `array-2d.c` code from last week?
2. When bubble sorting a random array, what fraction of the running time do you think is being spent on examining values (the loops + comparison)? Swapping values? Branch mispredictions?
3. What were the tests of bubble sort you ran under `perf`? (Likely 2 or 3 runs with different calls to `just_sort`.)
4. What fraction of the branches at the if in bubble sort were mispredicted when the array was random? When it was already sorted? Note that for an array of 20000, the `if` should execute 199,990,000 times.
5. How did the Perf report number of cache misses change with the larger 2D array "height"? Does it match your expectations?
6. How did the Cachegrind report number of cache misses change with the larger 2D array "height"? Does it match your expectations?

