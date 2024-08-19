# Lab 12
Use the [Compiler Explorer](https://godbolt.org/) to examine the way the compiler/optimizer deals with this code, specifically the following…

## The Optimizer
The provided `hailstone_length` is a recursive implementation of the "number of hailstone steps to get to one" problem from earlier in the semester.

Have a look at that function in Compiler Explorer with GCC and compiler options `-std=c17 -march=haswell` and see how it compiles. Compare that with the compilation at the different levels of compiler optimization: `-O1`, `-O2`, `-O3`. ❓

## Auto-Vectorization

Last week, one of the question proposed that the C "map polynomial" implementation was vectorized but the "dot product" wasn't. Let's check.

Compare the implementations of `map_poly_single_c` and `dot_single_c` at `-O3`. If the compiler is going to vectorize as we did last week, we'd hope to see it at -O3. ❓

In each case, the first step will be: **find the main loop** and ignore the rest. If you can identify the code that does most of the calculation, that's what needs your attention: it's usually the first loop. The following code that cleans up the "array size not divisible by SIMD register size" elements isn't going to be what takes the time.

The compilation of `dot_single_c` is deceptive. It seems to be using the SIMD instructions, but very differently that we did. Remember that it can't reorder the floating point operations and make a reasonable guess about what it's doing (without trying to unravel every instruction, which would be somewhere between painful and impossible).

## You can't spell -funsafe-math-optimizations without "fun"
When we wrote our vectorized assembly implementations, we didn't care about reordering floating point operations. Let's give the compiler the same benefit: add the `-funsafe-math-optimizations` option.

Now do those functions use SIMD instructions as you did last week? ❓.

Note: the [**vfmadd* instructions**](https://www.felixcloutier.com/x86/vfmadd132ps:vfmadd213ps:vfmadd231ps) are fused-multiply-add that do both a mulltiplication and addition as one operation.

## How much fun was it?
Re-run `timing.c` test from last week with `-O3 -funsafe-math-optimizations`.❓

## Questions
1. Without any compiler optimization, where are the local variables (`n` and `is_even`) stored in `hailstone_length`? How does that change at `-O1`?
2. For `hailstone_length` at `-O2`, how is `3*n+1` calculated?
3. For `hailstone_length`, a very surprising optimization occurs between `-O1` and `-O2`. What? (Hint: look for the recursive call.)
4. Was `map_poly_single_c` vectorized and `dot_single_c` not at `-O3`? How can you tell?
5. How did that change with `-funsafe-math-optimizations`?
6. How did the lab 11 performance change with `-funsafe-math-optimizations`? How did the C compare to the hand-written assembly or vectorclass implementations now?
