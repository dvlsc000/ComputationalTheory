# In-Depth Research and Explanations

This document provides extensive research and background for each task commonly encountered when working with low-level bit operations, cryptographic hash functions, prime number generation, fractional extraction of square roots, and analysis of leading zero bits in SHA-256 hashes.

---

## Table of Contents
1. [Task 1: Binary Representation](#task-1-binary-representation)  
   1.1 [Bitwise Rotation (`rotl` and `rotr`)](#bitwise-rotation)  
   1.2 [Bitwise Choose (`ch`)](#bitwise-choose-ch)  
   1.3 [Bitwise Majority (`maj`)](#bitwise-majority-maj)  
   1.4 [Test Case Descriptions](#test-case-descriptions-for-task-1)  
   1.5 [Research References](#research-references-for-task-1)

2. [Task 2: Kernighan and Ritchie Hash Function](#task-2-kernighan-and-ritchie-hash-function)  
   2.1 [Overview](#overview-kernighan-ritchie-hash)  
   2.2 [Mechanics and Rolling Hash Concept](#mechanics-and-rolling-hash-concept)  
   2.3 [Test Case Descriptions](#test-case-descriptions-kr-hash)  
   2.4 [Research References](#research-references-for-task-2)

3. [Task 3: SHA256 Padding Calculation](#task-3-sha256-padding-calculation)  
   3.1 [Overview](#overview-sha256-padding)  
   3.2 [Padding Steps](#padding-steps)  
   3.3 [Illustrative Example with `abc`](#illustrative-example-with-abc)  
   3.4 [Research References](#research-references-for-task-3)

4. [Task 4: Calculating the First 100 Prime Numbers](#task-4-calculating-the-first-100-prime-numbers)  
   4.1 [Trial Division Method](#trial-division-method)  
   4.2 [Sieve of Eratosthenes](#sieve-of-eratosthenes)  
   4.3 [Test Case Descriptions](#test-case-descriptions-for-prime-number-methods)  
   4.4 [Research References](#research-references-for-task-4)

5. [Task 5: Extracting 32 Bits of Fractional Parts of Square Roots for the First 100 Primes](#task-5-extracting-32-bits-of-fractional-parts-of-square-roots)  
   5.1 [Approach](#approach-for-task-5)  
   5.2 [Prime Generation Recap](#prime-generation-recap)  
   5.3 [Fractional Extraction Method](#fractional-extraction-method)  
   5.4 [Research References](#research-references-for-task-5)

6. [Task 6: Finding Words with Maximum Leading Zero Bits in Their SHA256 Hash](#task-6-finding-words-with-maximum-leading-zero-bits)  
   6.1 [Overview](#overview-for-task-6)  
   6.2 [Steps and Logic](#steps-and-logic-for-task-6)  
   6.3 [Research References](#research-references-for-task-6)

---

## Task 1: Binary Representation

### 1.1 Bitwise Rotation

Bitwise rotation operations are used to cyclically shift the bits of a 32-bit integer. They are critical in certain cryptographic algorithms (e.g., SHA-256) where pure shifts could lose information and where cyclical properties are needed.

1. **Left Rotation (`rotl`)**  
   - Shifts all bits to the left by *n* positions and wraps the overflow bits around to the right.  
   - Example Concept:  
     If you have the binary number `0001 0100` (decimal 20) and you rotate it left by 3 positions, you get `1010 0000`.

2. **Right Rotation (`rotr`)**  
   - Shifts all bits to the right by *n* positions and wraps the underflow bits around to the left.  
   - Example Concept:  
     If you have `0001 0100` (decimal 20) and you rotate it right by 3 positions, you get `1000 0010`.

#### Handling 32-Bit Unsigned Integers

When dealing with 32-bit integers:

- It is common to use bitwise masks (e.g., `& 0xFFFFFFFF`) to ensure the result stays within 32 bits.
- Rotations by a value greater than 32 should use the modulus operation (`n % 32`) to avoid unnecessary rotations.

---

### 1.2 Bitwise Choose (`ch`)

The **bitwise choose** operation (often seen in SHA-256) selects bits from two operands based on the bit pattern of a third operand:

- For each bit position:
  - If the corresponding bit in `x` is `1`, take the bit from `y`.
  - If it is `0`, take the bit from `z`.

Mathematically, this can be expressed as:
maj(x, y, z) = (x AND y) OR (x AND z) OR (y AND z)

### 1.4 Test Case Descriptions for Task 1

1. **Rotation Tests**  
   - **Rotate by 0**: Ensures no change.  
   - **Rotate by 32**: Full rotation brings the number back to its original value.  
   - **Rotate by n > 32**: Confirms rotation is handled modulo 32.

2. **`ch` Function Tests**  
   - **All 1s in `x`**: Should yield all bits from `y`.  
   - **All 0s in `x`**: Should yield all bits from `z`.  
   - **Mixed bits in `x`**: Verifies correct selection from `y` and `z`.

3. **`maj` Function Tests**  
   - **All inputs identical**: Should return that identical value.  
   - **Two inputs the same, one different**: Should reflect bits from the majority operands.  
   - **Mixed bit patterns**: Checks that each bit matches the majority logic.

These tests help confirm correctness and edge-case handling for each bitwise function.

---

### 1.5 Research References for Task 1

1. **Bitwise Rotate Right in Python**  
   [Stack Overflow Discussion](https://stackoverflow.com/questions/27176317/bitwise-rotate-right)

2. **Bitwise Operators in Python**  
   [Real Python](https://realpython.com/python-bitwise-operators/)  
   [GeeksforGeeks](https://www.geeksforgeeks.org/python-bitwise-operators/)


(Where the exact count of zero bytes ensures the total message + padding is 64 bytes short of the next block boundary.)

### 3.4 Research References for Task 3

- **Hash Function**  
  [GeeksforGeeks Hash Method](https://www.geeksforgeeks.org/python-hash-method/)

- **SHA-2 (Wikipedia)**  
  [SHA-2 Explanation](https://en.wikipedia.org/wiki/SHA-2)


---

## Task 4: Calculating the First 100 Prime Numbers

This task explores two popular algorithms to find prime numbers: the **Trial Division Method** and the **Sieve of Eratosthenes**.

### 4.1 Trial Division Method

- **Core Idea**: Check whether a candidate `n` is divisible by any integer from `2` to `sqrt(n)`. If no divisor is found, `n` is prime.  
- **Efficiency**: Simple to implement but can be slow for large `n`, especially when generating many primes.

#### Process Summary

1. Start from `2` (the first prime).  
2. For each candidate, check divisibility using numbers from 2 up to its square root.  
3. Once confirmed prime, add to the prime list.  
4. Continue until you have the desired count (e.g., first 100 primes).

### 4.2 Sieve of Eratosthenes

- **Core Idea**: Systematically mark multiples of each prime as non-prime, starting from 2.  
- **Efficiency**: Very fast for generating primes within a specific range.  
- **Steps**:
  1. Create a boolean array `is_prime` indexed from `0` to your `limit` (e.g., 550).  
  2. Mark indices `0` and `1` as not prime.  
  3. For each unmarked index `i`, mark all multiples of `i` (from `i*i` to the limit) as non-prime.  
  4. Remaining unmarked indices are primes.

### 4.3 Test Case Descriptions for Prime Number Methods

1. **Small Range** (e.g., up to 20): Compare with known prime list `[2, 3, 5, 7, 11, 13, 17, 19]`.  
2. **Medium Range** (e.g., up to 50): Confirms correctness for a slightly larger set, including more primes like `29, 31, 37, 41, 43, 47`.  
3. **Key Checks**:
   - Presence of known primes (e.g., `97` below 100).  
   - Absence of known composites (e.g., `99`).

Both methods, when tested, should yield the same prime sets for the same ranges, with the sieve typically outperforming trial division for larger ranges.

### 4.4 Research References for Task 4

- **Prime Generation (Design)**  
  [PythonHealthDataScience - Prime Algorithms](https://www.pythonhealthdatascience.com/content/01_algorithms/01_design/01_primes.html)

- **Sieve of Eratosthenes**  
  [GeeksforGeeks Explanation](https://www.geeksforgeeks.org/python-program-for-sieve-of-eratosthenes/)


---

## Task 5: Extracting 32 Bits of Fractional Parts of Square Roots

### 5.1 Approach for Task 5

1. **Generate the First 100 Primes**: Use the same methods described above (Trial Division or Sieve).  
2. **Compute the Square Roots**: For each prime number, calculate the square root.  
3. **Extract the Fractional Part**: Subtract the integer part (floor) from the square root to isolate the fractional portion.  
4. **Convert to Binary (32 bits)**: Multiply the fractional part by `2^32`, then take the integer portion. Convert that integer to its 32-bit binary representation.

### 5.2 Prime Generation Recap

- Either method (Trial Division or Sieve) can be used to generate the prime list.  
- Typically, to get the first 100 primes, we only need to go up to a small range (e.g., well under 600 for the Sieve of Eratosthenes).

### 5.3 Fractional Extraction Method

- **Fractional Part**: If `sqrt_val = floor_val + frac_val`, then `frac_val = sqrt_val - floor_val`.  
- **Binary Representation**: `int(frac_val * 2^32)` yields an integer whose bits correspond to the fractional portion.  
- **Zero-Padding**: Often, a 32-bit binary string is zero-padded on the left to ensure consistent length.

### 5.4 Research References for Task 5

- **Prime Numbers**  
  [List of First 100 Primes](https://prime-numbers.info/list/first-100-primes)

- **Square Roots in Python**  
  [GeeksforGeeks - `math.sqrt()`](https://www.geeksforgeeks.org/python-math-function-sqrt/)

- **Extraction of Fractional Part**  
  Various discussions on Stack Overflow regarding floating-point fractional bits.


---

## Task 6: Finding Words with Maximum Leading Zero Bits in Their SHA256 Hash

### 6.1 Overview for Task 6

This task searches for words (e.g., from an English word list) that produce SHA256 hashes with a large number of leading zeros in their **binary** representation. Leading zero bits in a SHA256 hash imply the hash is numerically small.

### 6.2 Steps and Logic for Task 6

1. **List of Words**: Define or obtain a list/set of English words.  
2. **Compute SHA256 Hash**: For each word, generate its SHA256 digest (a 256-bit value).  
3. **Convert to Binary**: The hash, originally in hexadecimal, is converted to a 256-bit binary string.  
4. **Count Leading Zero Bits**: Find how many bits from the left side of this binary string are zero.  
5. **Track Maximum**: Keep track of the largest count of leading zeros across all words.  
6. **Identify Winners**: Return the word(s) that achieve this maximum.

#### Example Use-Case

If your set of words is `["apple", "banana", "cat"]`, you:

- Compute each word’s SHA256 hash.  
- Convert to binary.  
- Count leading bits that are zero.  
- The word(s) with the most zero bits at the front “win.”

### 6.3 Research References for Task 6

- **Python `hashlib` Module**  
  [Python Docs: hashlib](https://docs.python.org/3/library/hashlib.html)

- **SHA-256 Algorithm**  
  [Wikipedia - SHA-2](https://en.wikipedia.org/wiki/SHA-2)

- **Python `bin()` Function**  
  [Python Docs: bin()](https://docs.python.org/3/library/functions.html#bin)

- **String Methods**  
  - `.zfill()` and `.lstrip()`: For padding and stripping zeros in the binary representation.


---

## Final Notes

Each task demonstrates a fundamental concept:

- **Bitwise Operations** are crucial for low-level cryptographic and hashing routines.  
- **Hash Functions** (K&R, SHA256) highlight practical hashing approaches and padding requirements.  
- **Prime Generation** explores computational complexity trade-offs between naive (Trial Division) and efficient (Sieve of Eratosthenes) methods.  
- **Fractional Bits Extraction** connects number theory (prime numbers) with floating-point manipulations (square roots), often used in cryptographic constants.  
- **Leading Zero Analysis** underscores how hash values can be ordered and how certain words might yield smaller (leading zeros) numerical hashes.


