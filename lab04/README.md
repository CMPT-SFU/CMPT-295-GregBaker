# Lab 4
## Is A Number Prime?

Write a function `is_prime` that takes a single (unsigned integer) argument (which you can assume is >1), and returns 1 if it's a prime number, and 0 otherwise. We aren't going to worry about a clever algorithm here: we will simply check every number from 2 to n-1 to see if it divides n:

```python
def is_prime(n):
    i = 2
    while i < n:
        if n % i == 0:
            return 0
        i += 1
    return 1
```

The `div` instruction divides unsigned integers and calculates their remainder, but it's unusual. It always divides the value `%rdx`:`%rax` (i.e. a 128 bit value with the high bits stored in `%rdx` and the lower bits in `%rax`) by its single operand. It always leaves the quotient in `%rax` **and** remainder in `%rdx`.

You can get `%rdi` divided by `%r8` in `%rax` and remainder in `%rdx` like this. The first two instructions set `%rdx`:`%rax` to the value from `%rdi` (by putting bunch of leading zeros in `%rdx`).

```Assembly
mov $0, %rdx
mov %rdi, %rax
div %r8
```
## Unsigned Overflow
Write an assembly function `largest_power_unsigned` that takes an unsigned integer as its argument and returns the **largest power** of that number that's representable as a 64-bit unsigned integer.

In order to find that value, you need to repeatedly multiply by _n_ until you see an unsigned overflow and return the value _before _you saw the overflow.

The value you return _p_ should be a power of n, so n<sup>i</sup> for some _i_, such that p < 2<sup>64</sup> and np >=  2<sup>64</sup>. (But you can't detect it with math: you need to calculate and watch the carry flag.)


## Signed Overflow
Write a function `overflowing_subtract(a, b)` that take two **signed** integer arguments. It should usually return `a-b`, unless that calculation is a signed overflow then it should return zero.

## Recursion
Write an assembly function `dumb(a, b)` that takes two unsigned integer arguments and implements this recursive calculation (that's called "dumb" because it does a dumb calculation that makes no sense, but that's the task):

```Python
def dumb(a, b):
    if a == 0:
        return b + 1
    elif b == 0:
        return a
    else:
        return a + a + b + dumb(a - 1, b - 1)
```
You must use the stack to store the `a` and `b` values across the recursive call.

