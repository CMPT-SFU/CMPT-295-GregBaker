# Lab 11
We're switching to C++ this week so we can use vectorclass.
## SIMD Assembly
Now that we know about the x86 vector/SIMD instructions, let's use them with problems we're familiar with.

In `lab11.S`, copy your dot product and polynomial evaluation implementations from last week: `dot_double`, `dot_single`, `map_poly_double`, `map_poly_single`.

Write functions that produce the same results, but using the SIMD instructions and `%ymm` registers: `dot_double_vec`, `dot_single_vec`, `map_poly_double_vec`, `map_poly_single_vec`. You can assume the array length is divisible by the 4 (for double-precision) or 8 (for single-precision).

You will need the `vbroadcastsd` and `vbroadcastss` instructions (which weren't mentioned in lecture) which broadcast one double-/single-precision value to all of the fields in a vector register.

## SIMD with Vectorclass
Create a C++ file `lab11_vc.cpp`. In it, repeat the above, creating C++ implementations `dot_double_vc`, `dot_single_vc`, `map_poly_double_vc`, `map_poly_single_vc` that use the [vectorclass library](https://github.com/vectorclass/version2) to implement this logic using SIMD instructions accessed from C++.

## Time It
The provided `timing.cpp` provides some timing tests on reasonably-sized arrays. Have a look. How does your code compare to what the compiler wrote? (Use `-O3` to give the compiler its best chance.) ❓
Mini-Project


## Questions
1. Relative to your assembly code last week, how much did the "dot product" and "map polynomial" implementations speed up when using the vector instructions?
2. On the two problems, what was the relative speedup of vectorized implementations on single-precision floating point values, over double-precision?
3. When timing your assembly (and vectorclass) implementations and the implementations created by the compiler, you likely saw that for the "dot product" problem, the C implementation performed more like the non-vectorized assembly. For the "map polynomial" problem, the C implementation performed more like the vectorized assembly. Why was the compiler able to vectorize one but not the other?
