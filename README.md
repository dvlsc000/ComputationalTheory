# Task 1: Bit-level Operations on 32-bit Unsigned Integers

This task implements four functions in Python to perform essential bit-level operations on 32-bit unsigned integers. These operations are widely used in cryptographic algorithms (e.g., SHA-256) and low-level data processing.

## Overview

The project provides implementations for:

- **rotl(x, n=1)**: Left bit rotation.
- **rotr(x, n=1)**: Right bit rotation.
- **ch(x, y, z)**: Bitwise "choose" function.
- **maj(x, y, z)**: Bitwise "majority" function.

Each function ensures that the values are treated as 32-bit unsigned integers by applying masking with `0xffffffff` throughout the computation. Additionally, they handle rotations greater than 32 by applying modulo arithmetic.

---

## Function Descriptions

### rotl(x, n=1)

**Purpose:**  
Rotate the bits of a 32-bit unsigned integer `x` to the left by `n` positions.

**Implementation Details:**

1. **Masking:**  
   - `x` is masked with `0xffffffff` to ensure it is within the 32-bit range.
2. **Modulo Operation:**  
   - The number of rotations `n` is reduced modulo 32 (`n %= 32`) to handle values greater than 32.
3. **Bit Rotation:**  
   - Shifts `x` left by `n` bits and right by `(32 - n)` bits, combining the results with bitwise OR.
4. **Final Masking:**  
   - The result is masked again with `0xffffffff` to guarantee a 32-bit output.

**Example:**

```python
rotl(0x12345678, 4)  # Expected output: 0x23456781
