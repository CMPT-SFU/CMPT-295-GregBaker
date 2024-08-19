# Mini Project
This project is individual work.

## The Task
For this mini-project (or large-assignment, or however you prefer to think about it), you will be using the concepts we have covered in the course to evaluate several pieces of code.

In particular, that will involve some of these (or possibly other factors):

* how well the code can be converted to assembly by a compiler. Some code is structured so that the compiler can transform it into extremely efficient assembly, but not always. Does optimization level affect some code more than others?
* the instructions that can be used (e.g. vector instructions) in their low-level implementation. Perhaps this is dependent on the details of the processor architecture (e.g. has 128- vs 256-bit SIMD registers).
* memory locality. For larger data structures, how does the algorithm/code perform when data is too large to (or arranged in memory so it doesn't) fit in L1/L2/L3 cache?
* branch predictability. Where there are branches (conditionals or non-infinite loops), are they predictable to the CPU?

In particular, remember that your evaluation for this project should be more than "code goes fast".

## Choices You Need To Make
The topic of your exploration is yours. You need to find a few algorithms and/or multiple implementation of a specific algorithm to evaluate.

### Problem/Algorithm(s)
You can't compare the performance of every possible task that might be done in a programming language: you need to pick a small number to examine closely. You need to look at problems that can be "scaled up" so the execution takes long enough to effectively measure. (e.g. the problem "add two integers" is never going to be interesting enough to see differences in implementations.)

If you want to look carefully at memory performance (memory locality/cache misses), a problem that requires large arrays is probably necessary. That could be one of:
* Dot product (of large arrays/vectors, but since this has been done in lab exercises, we'll expect more work/analysis somewhere else).
* Fibonacci numbers, maybe with the worst algorithm in the world.
* Numerical integration.
* Quicksort, Mergesort, Insertion sort, … on various inputs (random, already-sorted, reverse-sorted, partially-sorted, many-duplicate values).
* Modern highly-tuned sorting implementations: pdqsort, Glidesort, crumsort, possibly comparing to language default sorts.
* Multiply two matricies.
* Depth-first vs breadth-first search of a tree.
* Graph algorithms: topological sort, Dijkstra's algorithm.
* something else.

None of those have been particularly carefully thought through: you might explore and find out "they're pretty much all the same", but that's still a conclusion.

### Implementations
You can't really test an *algorithm* for detailed performance, or memory locality. They need to be implemented for that. Some possible implementations you might be able to evaluate (keeping in mind that not all of these apply to every problem/algorithm):

* Find an implementation in [Rosetta Code](https://rosettacode.org/wiki/Rosetta_Code) or similar.
* Implement it yourself in what you would consider "normal" code.
* Implement it yourself in carefully optimized code.
* Any of those, but working with a different data structure (e.g. C array vs C++ vector vs linked list, or Python list vs numpy array).
* Find an implementation in a textbook.
* Implement it yourself in C++ with Agner Fog's [Vector Class Library](https://github.com/vectorclass/version2) for SIMD operations.
* Find an implementation in a code library (either the language's built-in "standard" library, or a third-party library).

You might choose a single implementation of several (related) algorithms, or several implementations of a single algorithm.

## Choices You Don't Need To Make
… but could if it feels interesting to you.

### Choice of Operating System
Default choice: Linux.

Comparing performance of specific code on Windows vs Linux vs MacOS is probably **not** interesting: the same CPU should run the same code the same way (if compiled with a similar compiler). But, if you would like to do this project under Windows or MacOS, that's fine.

### Choice of Language(s)
Default choice: C or C++.

You may want to look at other language(s). Learning a new programming language for this project would be interesting, but it's not exactly on-topic. But if you'd like to do a cross-language comparison…

It probably makes sense to focus on languages that compile to native object code: Assembly, C, C++, Rust. (More obscure languages with native x86 compilation: Fortran, Haskell, Nim.)

It might be interesting to compare languages with [Just-In-Time compilers](https://en.wikipedia.org/wiki/Just-in-time_compilation): JavaScript with Node, Python with PyPy.

You could also compare languages/tools that compile to bytecode and run in a (process) virtual machine, possibly also with Just-In-Time compilation (like the Java virtual machine, .NET CLR). Those are real ways people run code: how do they compare to native compilation?

You may find interesting differences in the built-in data types in different languages. e.g. Python lists feel a little like arrays, but their implementation is quite different. That probably has memory locality implications.
### Choice of CPU Architecture(s)
Default choice: x86-64.

Comparing the performance of a few pieces of code on modern x86-64 and ARM (i.e. M1/M2 Macs) might be interesting. Assembly code for other architectures is different than for x86-64, but it's not so radically different that you won't be able to read it with a little investigation of instructions you don't know.

## Data
You'll need to run the code and see what happens, so you can report on it. See the "Tools" slides and Lab 9 for more detail. Some things you might want to look into…

### Different Inputs
Code will behave differently depending on the input you give it: small, large, larger data sets; "good" vs "unexpected" input;

Choose a few realistic examples of input that can be given to your code. You will need inputs that are "large" enough to have interesting performance. Most code is just fine on small inputs where only take a few microseconds are needed to complete.

### Running Time
Measuring the running time of code can be tricky (as you have seen though the semester). Make sure your comparisons are on the same processor (in the same powered low/high power state, not thermal throttled, etc). Depending on the combination of technologies, the way you measure running time might vary:
* Using the `rdtsc` instruction (`__rdtsc` in C) before and after your call.
* Using a high-resolution clock time, likely [clock_gettime](https://stackoverflow.com/questions/6749621/how-to-create-a-high-resolution-timer-in-linux-to-measure-program-performance/6749766#6749766) if you're in C.
* If you're using different languages, the [Linux time command](https://linuxize.com/post/linux-time-command/) to time the whole program execution

### Memory and CPU Use

There are various tools you can use that will report on a program's usage of processor, cache, memory, how the branch predictor did.

* [Perf](https://perf.wiki.kernel.org/index.php/Main_Page): `perf record ./program`
* [Valgrind](https://valgrind.org/): `valgrind ./program`
* [The time command](https://man7.org/linux/man-pages/man1/time.1.html): `/usr/bin/time -v ./program`

## Your Report

Write a 2–3 page summary of what you found (single-spaced, reasonably-default formatting).

You should focus on the problems relevant to this course as described above ("The Task"). You should describe (at least) …

* the code you're evaluating,
* how you evaluated it,
* the results of whatever tests you ran,
* what you learned about the code.


