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

---

## Task 2: Kernighan and Ritchie Hash Function

### 2.1 Overview (Kernighan & Ritchie Hash)

This hash function originates from *The C Programming Language* by Brian Kernighan and Dennis Ritchie. In C, it looks like:

```c
unsigned hash(char *s) {
    unsigned hashval;
    for (hashval = 0; *s != '\0'; s++)
        hashval = *s + 31 * hashval;
    return hashval % 101;
}

