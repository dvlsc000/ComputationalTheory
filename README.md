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

7. [Task 7: Turing Machines](#task-7-turing-machines)

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

**Mathematical Expression:**
maj(x, y, z) = (x AND y) OR (x AND z) OR (y AND z)

### 1.3 Bitwise Majority (maj)

The bitwise majority function selects each output bit based on the majority value of the corresponding bits in three input integers. For each bit position:

- If at least two out of the three bits (from `x`, `y`, and `z`) are 1, the result bit is 1.
- Otherwise, the result bit is 0.

This operation is commonly used in cryptographic algorithms such as SHA-256, where it contributes to the diffusion and mixing of state values.

**Mathematical Expression:**
maj(x, y, z) = (x AND y) OR (x AND z) OR (y AND z)

---

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

---

## Task 2: Kernighan and Ritchie Hash Function

### 2.1 Overview (Kernighan & Ritchie Hash)

Translated conceptually to Python, it processes each character of the string by:

1. Multiplying the current hash by 31.  
2. Adding the ASCII value of the current character.  
3. Taking a modulo (e.g., 101) at the end or at each step.


### 2.2 Mechanics and Rolling Hash Concept

- **Rolling Hash**: Each new character updates the existing hash in a rolling fashion, making it computationally efficient for scanning data (e.g., in string matching).  
- **Hash Range**: Taking the result modulo 101 confines the hash to values `0–100`.  
- **Collision Probability**: Since 101 is small, collisions are relatively likely in practice. For small sets or learning examples, however, this is sufficient as a demonstration of basic hash functions.


### 2.3 Test Case Descriptions (KR Hash)

- **Common Words**: Testing with strings like `"hello"`, `"world"`, `"hash"`, `"function"` ensures that we observe how the hash distributes different inputs.  
- **Consistency Check**: Verifying the same string multiple times yields the same hash value.  
- **Empty String**: Testing an empty string ensures we handle the boundary condition properly (hash often becomes `0`).

### 2.4 Research References for Task 2

- **Kernighan and Ritchie's Hash Function**  
  [CodeProject Article](https://www.codeproject.com/Articles/32829/Hash-Functions-An-Empirical-Comparison)

- **General Purpose Hash Function Algorithms**  
  [Partow.net](https://www.partow.net/programming/hashfunctions/)

- **Hash Functions and Hash Tables**  
  [Linux.IME.USP.br (PDF)](https://linux.ime.usp.br/~brelf/mac0499/monografia.pdf)

---

## Task 3: SHA256 Padding Calculation

### 3.1 Overview (SHA256 Padding)

In SHA-256 (and other SHA-2 variants), the message is padded to a length that is a multiple of 512 bits. The padding rules are:

1. Append a single `1` bit (expressed as `0x80` in the first padding byte).  
2. Append enough `0` bits (or bytes) so that the total length (message + padding + 8 extra bytes for length) is a multiple of 64 bytes.  
3. Finally, append the length of the original message (in bits) as a 64-bit big-endian integer.

### 3.2 Padding Steps

1. **Message Length**: Compute the length of the message in bytes, then multiply by 8 to get the bit length.  
2. **Append `0x80`**: This is the mandatory `1` bit at the start of the padding.  
3. **Append Zeros**: The number of zero bytes depends on making the final length a multiple of 64:

   \[
   \text{zero\_bytes} 
   = \bigl(56 - (\text{original\_length} + 1) \bmod 64 \bigr) \bmod 64
   \]

   - `+1` accounts for the `0x80` byte.  
   - `+8` would account for the length field that must fit after the zeros.

4. **Append 64-Bit Length**: The final 8 bytes encode the original message length (in bits) in big-endian order.

### 3.3 Illustrative Example with `abc`

- **Original message length**: 3 bytes → 24 bits.  
- **Append `0x80`**: `80` in hex.  
- **Append enough zeros** until the total length is `56 (mod 64)`.  
- **Append `24` (decimal) = `0x18`** in big-endian across 8 bytes.

### 3.4 Research References for Task 3

- **Hash Function**  
  [GeeksforGeeks Hash Method](https://www.geeksforgeeks.org/python-hash-method/)

- **SHA-2 Explanation**  
  [SHA-2 Explanation](https://en.wikipedia.org/wiki/SHA-2)

---

## Task 4: Calculating the First 100 Prime Numbers

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

### 4.4 Research References for Task 4

- **Prime Generation (Design)**  
  [PythonHealthDataScience - Prime Algorithms](https://www.pythonhealthdatascience.com/content/01_algorithms/01_design/01_primes.html)

- **Sieve of Eratosthenes**  
  [GeeksforGeeks Explanation](https://www.geeksforgeeks.org/python-program-for-sieve-of-eratosthenes/)

---

## Task 5: Extracting 32 Bits of Fractional Parts of Square Roots for the First 100 Primes

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
  [GeeksforGeeks - math.sqrt()](https://www.geeksforgeeks.org/python-math-function-sqrt/)

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
  [SHA-256 Explanation](https://en.wikipedia.org/wiki/SHA-2)

- **Python `bin()` Function**  
  [Python Docs: bin()](https://docs.python.org/3/library/functions.html#bin)

- **String Methods**  
  - `.zfill()` and `.lstrip()`: For padding and stripping zeros in the binary representation.

---

## Task 7: Turing Machines

### Overview
In this task, we design a simple Turing Machine that adds 1 to a binary number written on its tape. The machine starts at the left-most non-blank symbol and treats the right-most symbol as the least significant bit (LSB). The Turing Machine simulates the binary addition (increment) process as follows:

1. **Traverse to the LSB:**  
   The machine moves right until it reaches the right-most digit of the binary number.

2. **Addition Operation:**  
   - If the current (LSB) symbol is `0`, it is replaced with `1` and the machine halts (no carry is needed).  
   - If the symbol is `1`, it is changed to `0` (indicating a carry), and the machine moves one cell to the left to continue processing the carry.  
   - If the carry propagates past the left-most digit, the machine prepends a `1` to the tape.

3. **Halting:**  
   The machine halts after the addition is complete and the tape now reflects the incremented binary number.

#### Example
- **Input Tape:** `100111`  
- **Expected Output Tape:** `101000`

### References
1. **Turing's Original Paper:**  
   Turing, A. M. (1936). *On Computable Numbers, with an Application to the Entscheidungsproblem*.  
   [PDF available here](https://www.dcs.gla.ac.uk/~pat/algorithms/book/turing.pdf)

2. **GeeksforGeeks – Turing Machine Introduction:**  
   [Turing Machine – Introduction](https://www.geeksforgeeks.org/turing-machine-introduction/)

3. **Tutorials Point – Turing Machine:**  
   [Turing Machine Explanation](https://www.tutorialspoint.com/automata_theory/turing_machine.htm)

---

## Final Notes

Each task demonstrates a fundamental concept:

- **Bitwise Operations** are crucial for low-level cryptographic and hashing routines.  
- **Hash Functions** (K&R, SHA256) highlight practical hashing approaches and padding requirements.  
- **Prime Generation** explores computational complexity trade-offs between naive (Trial Division) and efficient (Sieve of Eratosthenes) methods.  
- **Fractional Bits Extraction** connects number theory (prime numbers) with floating-point manipulations (square roots), often used in cryptographic constants.  
- **Leading Zero Analysis** underscores how hash values can be ordered and how certain words might yield smaller (leading zeros) numerical hashes.  
- **Turing Machines** illustrate fundamental concepts in computability and machine simulation with a simple, yet effective, binary addition example.
