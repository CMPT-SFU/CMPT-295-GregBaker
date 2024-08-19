# Lab 7
## Random Number Generator

For this question, you'll create a pseudo-random number generator (specifically, a linear congruential generator) function in `lab7.S`.

Our LCG's behaviour will be like this:

```Python
seed = 0
def randint():
    #global seed
    seed = seed * 6364136223846793005 + 1442695040888963407
    #seed = seed % (2**64)
    return seed
```

The commented-out lines would be necessary to express this as actual Python code (which needs global variables explicitly declared, and isn't limited to 64-bit integer arithmetic), but these are not necessary in an assembly translation of the logic.

Our random number generator will always start with the same seed (0). That means it will always generate the same sequence of "random" numbers, but that's fine for what we're doing.

The random seed will need to be a 64-bit integer in static memory, reserved and initialized in a `.data` section.

The constants in the calculation are larger than 32-bit constants that are allowed in most instructions, so the only way to use them is with `movabs` to first get the value into a register:

`movabs $6364136223846793005, %r8`

With those hints, write a function `randint` in `lab6.S` that generates pseudo-random unsigned integers as described.

## Random Array
Write an assembly function `fill_array` that takes two arguments: a 64-bit signed integer array pointer and the number of elements. It should fill the array with random values (generated using your `randint`).

We don't want our array values to be too big, so we'll keep the array elements in the range -128–127 (but still as 64-bit integers). Hint:

```Assembly
call randint
and $0xff, %rax
sub $128, %rax
```

## Dot Product

Write an assembly function `dot` that takes two (64-bit signed integer) array pointers and a length (assumed to be the same for both arrays). It should return the dot product of the two arrays, i.e.

`arr1[0]*arr2[0] + arr1[1]*arr2[1] + ⋯ + arr1[n]*arr2[n]`

## Array of Structs
Instead of working with pairs of arrays, maybe we should work with arrays of pairs. The provided `lab7.h` defines this struct:

```C
typedef struct {
   int64_t a;
   int64_t b;
} pair64_t;
```
In C, we know that **array elements** are guaranteed to be adjacent in memory, but **struct members** are also guaranteed to be arranged in memory in the order they're declared. We generally expect them to be adjacent, so this struct should take 16 bytes: the first 8 bytes are `a` and the next 8 are `b`. (It's actually [a little more complicated than that](https://stackoverflow.com/questions/2748995/struct-memory-layout-in-c/2749096#2749096) because of padding and alignment, but we can ignore those for now.)

Repeat the dot product calculation, but taking a single array of `pair64_t` (and a length) as arguments.

## Speed
The included `timing.c` will do some timing of your implementations, as well as C implementations in `lab7c.c`. Have a look. ❓

## Questions

Answer these questions in a text file `answers.txt`.
1. What commands did you use to compile/assemble and run your code for this lab?
2. How did the running times compare for the various dot product implementations?

