# ADD - Arithmetic Operations and EFLAGS

## Program 1: add1.asm

### Operation

The program loads two 8-bit values:

```text
num1 = 120
num2 = 10
```

It performs:

```text
120 + 10 = 130
```

Binary:

```text
  01111000
+ 00001010
----------
  10000010
```

The result `130` is stored in the 8-bit `AL` register.

### GDB Result

```text
EAX = 0x82 = 130
EFLAGS = 0xa96 [ PF AF SF IF OF ]
```

### Flag Analysis

| Flag | Status  | Explanation                                                                                                                  |
| ---- | ------- | ---------------------------------------------------------------------------------------------------------------------------- |
| CF   | Cleared | There is no carry out of the 8-bit result. The unsigned result 130 fits within 0–255.                                        |
| PF   | Set     | The low byte `10000010` contains two `1` bits. Since two is even, PF is set.                                                 |
| AF   | Set     | The lower nibbles `1000 + 1010` produce a carry from bit 3 to bit 4, so AF is set.                                           |
| ZF   | Cleared | The result is 130, not zero.                                                                                                 |
| SF   | Set     | The most significant bit of `10000010` is 1, so SF is set.                                                                   |
| OF   | Set     | Both operands are positive, but 130 is greater than the maximum signed 8-bit value of 127. Therefore signed overflow occurs. |

### Explanation

The operation produces `10000010`, which is 130 as an unsigned value. There is no unsigned carry, so CF is cleared. The result is not zero, so ZF is cleared. The most significant bit is 1, causing SF to be set. Because two positive signed values produce a mathematical result greater than 127, signed overflow occurs and OF is set. The result contains two `1` bits, so PF is set. The lower-nibble addition also produces a carry, so AF is set.

---

## Program 2: add2.asm

### Operation

The program loads two 16-bit values into `AX`:

```text
num1 = 32000
num2 = 500
```

It performs:

```text
32000 + 500 = 32500
```

In hexadecimal:

```text
32000 = 0x7D00
500   = 0x01F4
32500 = 0x7EF4
```

Binary result:

```text
0111 1110 1111 0100
```

### GDB Result

```text
EAX = 0x7EF4 = 32500
EFLAGS = 0x202 [ IF ]
```

### Flag Analysis

| Flag | Status  | Explanation                                                                                         |
| ---- | ------- | --------------------------------------------------------------------------------------------------- |
| CF   | Cleared | 32500 fits within the unsigned 16-bit range of 0–65535, so there is no carry out of bit 15.         |
| PF   | Cleared | The lowest byte is `11110100`. It contains five `1` bits, which is an odd number, so PF is cleared. |
| AF   | Cleared | The lower nibbles are `0000 + 0100`, which produce no carry from bit 3 to bit 4.                    |
| ZF   | Cleared | The result is 32500, not zero.                                                                      |
| SF   | Cleared | The most significant bit of the 16-bit result `0111111011110100` is 0, so the result is positive.   |
| OF   | Cleared | 32500 is within the signed 16-bit range of -32768 to 32767, so no signed overflow occurs.           |

### Explanation

The addition produces `0x7EF4`, which is 32500. The result is within both the unsigned 16-bit range and the signed 16-bit range. Therefore, there is no carry and CF is cleared. Since the result is not zero, ZF is cleared. The most significant bit is 0, so SF is cleared. The result is also within the signed 16-bit range, so OF is cleared. The lowest byte `11110100` contains five `1` bits, which is odd, so PF is cleared. There is no carry between bit 3 and bit 4, so AF is cleared.

