# Lab 6
## C-Style String Length
The `strlen` function in C calculates the length of a C string: a sequence of bytes that's terminated by a null byte ('\0'). Write an assembly function `strlen_mine` in `lab6.S` that does this: takes a pointer to a byte array and counts the number of bytes before getting to a null (zero) byte.

Remember that we're working with bytes here (not 64-bit values), and that if you have a memory address (pointer) in a register, you can follow the pointer to inspect that memory location by putting the register name in parenthesis. i.e. this is probably a useful comparison to make (adjusting the register name as you like):

```Assembly
cmpb $0, (%rdi)
```

The provided `tests.c` contains some tests of this and the next function. It `#includes` the data from `test_strings.c` as test cases. The `test_strings.c` does not need to be included on the command line:

```
gcc -Wall -Wpedantic -std=c17 -march=haswell -O3 tests.c lab6.S \
&& ./a.out
```
## UTF-8 String Length
C strings are not typically treated like encoded Unicode characters, but they could be. Write an assembly function `strlen_utf8` in `lab6.S` that calculates the number of Unicode characters in a byte array, treating it as UTF-8-encoded text.

This will be similar to the previous question, except you should **not** count bytes that are UTF-8 continuation bytes: any byte in the form 10xxxxxx is a continuation byte. You will need to extract those specific bits from each byte to see if they are the "right" value for a continuation byte.

If you want to examine specific bits of an integer, you can construct a value to bitwise-AND with to keep exactly the bits you care about. In this case, 11000000:

```
  0bxxyyyyyy
& 0b11000000
= 0bxx000000
```

And then make a comparison for equality.

## UTF-8 Characters
Write a **C function** (yes, we're drifting back toward C) `decode_utf8` in `utf8.c` that takes a pointer to a byte array (i.e. a `char*`), decodes the UTF-8 encoded text (until finding the null byte marking the end of the string) to character numbers (i.e. the character '`a`' is 97).

For each Unicode character in the string, call the provided `report_character` with the Unicode character number and the number of bytes it took to express it in UTF-8. So, finding a '`ç`' ([character number 231](https://symbl.cc/en/00E7/), encoded as two bytes) should cause the equivalent of:

`report_character(231, 2);`

Extracting the UTF-8 character data will require inspecting the first byte of each character to see which row of the [encoding table](https://en.wikipedia.org/wiki/UTF-8#Encoding) it's in, then looking at the next 0–3 bytes, extracting the bits you need (the ones marked "x" in the table), and turning them into a 32-bit integer (because that's the argument to `report_character`).

Combining the bits you need out of each byte will likely be a combination of bitwise-AND (`&`) to get the bits you want, shifting (`<<` or `>>`) to move them to the right position, and bitwise-OR (`|`) to combine.

The provided `possible-template.c` could be used as a basis for your `utf8.c` if you like (and you certainly free to modify as you like). It will at least run and let you work incrementally from incorrect to correct results.

The provided `utf8-test.c` will call your `decode_utf8` on several strings and produce HTML output of the results. It also uses data from `test_strings.c` as tests cases.

You can assume valid UTF-8 encoded text (e.g. after a 110xxxxx byte, there will be exactly one continuation byte before the next character starts).

### Option 1: Just Run My Code

The main function of `utf8-test.c` produces HTML output. You can look at that in the terminal, or redirect it to a file with a ">" on the command line, like this:

```
g++ -Wall -Wpedantic -std=c++14 -march=haswell -O3 utf8.c utf8-test.c \
&& ./a.out > output.html
```

Then open `output.html` in your favourite web browser.

[Note: that command compiles with the C++14 standard so you can use [binary literals](https://www.geeksforgeeks.org/binary-literals-in-c14-with-examples/) like `0b11111111` if you want.]

### Option 2: Greg spent too long on this and doesn't want to throw away the code…

This method will use your function the same way and produce similar output, but with a description of each character. It requires libraries that I assumed were part of the standard Linux installation, but apparently not. In a Debian/Ubuntu/Mint distribution, you need the python3-gdbm and libgdbm-dev packages. They should be available in CSIL Linux.

I have provided a Python program `create_character_db.py` that downloads from files from the [Unicode Character Database](https://www.unicode.org/Public/UCD/latest/) and produces a database file (with [GDBM](https://www.gnu.org.ua/software/gdbm/), basically a disk-based hash table) of Unicode character number to descriptions of the characters.

Then set the `USE_GDBM` preprocessor macro to turn on the conditional blocks in `utf8-test.c` that will use the character database (the `-DUSE_GDBM` on the command line).

```Python
python3 create_character_db.py # one-time setup
g++ -Wall -Wpedantic -std=c++14 -march=haswell -O3 -DUSE_GDBM utf8.c utf8-test.c -lgdbm \
&& ./a.out > output.html
```

For all that work, you will get slightly different output. 🎉

