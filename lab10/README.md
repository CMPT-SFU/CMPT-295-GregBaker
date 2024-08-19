# Lab 10
## Dot Product, again
The dot product (pairwise product, and sum) that we did in Lab 7 makes more sense on floating point values.

In `lab10.S`, write a function `dot_double` that calculates the dot product of two arrays, as before but on double-precision floating point arrays.

An equivalent `dot_double_c` has been provided in lab10c.c.

## Polynomial evaluation, again
We previously wrote a function that evaluated a cubic polynomial on a single x value, using the equivalent of this expression: `x*(x*(a*x + b) + c) + d`.

This time, we want to apply this operation to **an array of double x values**, writing the result to another (equally-sized) array. That is, for each array element, do the above calculation and put the result in the corresponding position in the "output" array.

In `lab10.S`, write a function `map_poly_double` with this signature (the coefficients will be constant):

```
void map_poly_double(double* input, double* output, uint64_t length, double a, double b, double c, double d);
```

Equivalent `map_poly_double_c1` and `map_poly_double_c2` have been provided, with the two expressions we used in the previous exercise, for comparison.

### That, but Single Precision
Maybe single-precision floating point operations are faster?

In `lab10.S`, write functions `dot_single` and `map_poly_single` that are equivalent to the above but work on single-precision floating point values (`float`). This should be as simple as swapping double-precision instructions for their single-precision equivalents, and changing the element size from 8 to 4.

Some single-precision instructions that I found useful: `movd`, `addss`, `mulss`.

As before, there are `dot_single_c` and `map_poly_single_c` in `lab10c.c` for comparison.

### Time It
We have seen some students where a combination of the details of their bubble sort implementation (but not if tiny changes are made) **and** `GCC` (not `Clang`) **and ** `-O3` (not `-O2`) give very weird timing results. The problem seems to be some mis-applied compiler optimization in some very specific case.

If what you're seeing makes no sense, try switching to `-O2` at the command line: if everything is sane, there should be no difference between `-O2` and `-O3` on this code.

### Is it really branch mispredictions?

The provided `timing.c` provides some timing tests on reasonably-sized arrays. Have a look. How does your code compare to what the compiler wrote? (Use `-O3` to give the compiler its best chance.) ❓

### Why not x87
As mentioned in lecture, we aren't using the x87 instructions in this course. Why not? It seems like there are a lot of programmer-friendly instructions there (loading constants, trigonometry, logarithms, they work on a stack and everybody loves stacks).

This function (with C signature `void sin_x87(double* input, double* output, uint64_t length`)) calculates the sine of each element of an array of `double` and fills another (equally-sized) array with the results. You don't need to worry about the details, except that it uses x87 floating point instructions to do the calculation. You can copy it into your `lab10.S`.

```Assembly
sin_x87:
    mov $0, %rcx
s87_loop:
    cmp %rdx, %rcx
    jae s87_ret
    fldl (%rdi, %rcx, 8)
    fsin
    fstpl (%rsi, %rcx, 8)
    inc %rcx
    jmp s87_loop
s87_ret:
    ret
``` 
Create a program `sin.c` and **write a C implementation** `sin_stdlib` that does the same operation, but using the C [standard library's sin function](https://cplusplus.com/reference/cmath/sin/). You will have to include `math.h` and add `-lm` to your compiling/linking command.

The `sin` function you're calling isn't using any trig instructions, but is doing **something different**. Is it faster? ❓

You have not been provided with testing or timing code for this part of the exercise. That is deliberate. **Write a main function** in your `sin.c` that produces output relevant to testing/timing the implementations (i.e. what you need to convince yourself your code is correct and answer the question below). The command to compile it should be like this:

```gcc … lab10c.c lab10.S sin.c -lm```

## Questions
Answer these questions in a text file `answers.txt`.
1. What was the running time of the dot product implementations? Assembly vs the compiler, and single- vs double-precision?
2. Same question, but for the polynomial evaluation? (Prediction: the differences should be much more obvious here.)
3. What is the relative running time of the x87-based sine calculation vs the C implementation (that uses its own implementation of the function)?


