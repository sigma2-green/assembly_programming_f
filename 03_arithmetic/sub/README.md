# Subtraction Programs

This folder contains two x86 assembly language programs that demonstrate subtraction using 8-bit and 16-bit registers.

## Programs

* `sub1.asm` - 8-bit subtraction
* `sub2.asm` - 16-bit subtraction

Both programs were assembled using NASM and linked as 32-bit Linux executables. GDB was used to inspect the results and CPU flags immediately after the subtraction instruction.

---

# 1. sub1.asm - 8-bit Subtraction

## Program Description

The program stores two 8-bit numbers:

```asm
num1 db 50
num2 db 80
```

It loads `num1` into the `AL` register and subtracts `num2`:

```asm
mov al, [num1]
sub al, [num2]
```

Therefore, the operation is:

```text
50 - 80 = -30
```

Since `AL` is an 8-bit register, the result is stored using two's complement representation.

The result is:

```text
-30 = 11100010₂ = E2₁₆
```

GDB displays the value as:

```text
EAX = 0xe2 = 226
```

The value `226` is the unsigned interpretation of the same 8-bit bit pattern. As a signed 8-bit value, `0xE2` represents `-30`.

## GDB Result

The following commands were used:

```gdb
break *0x0804900b
run
info registers eax
p/x $eax
info registers eflags
```

The result was:

```text
eax      0xe2    226
$1       0xe2
eflags   0x287   [ CF PF SF IF ]
```

## Flag Analysis

| Flag | Status  | Explanation                                                                                  |
| ---- | ------- | -------------------------------------------------------------------------------------------- |
| CF   | Set     | 50 is smaller than 80 when treated as unsigned values, so the subtraction requires a borrow. |
| PF   | Set     | The result `11100010` contains four `1` bits. Four is even, so the parity flag is set.       |
| AF   | Cleared | The lower nibbles are `0010 - 0000`, so there is no borrow from bit 3 to bit 4.              |
| ZF   | Cleared | The result is not zero.                                                                      |
| SF   | Set     | The most significant bit of the 8-bit result is `1`, indicating a negative signed result.    |
| OF   | Cleared | `50 - 80 = -30`, which is within the signed 8-bit range of `-128` to `127`.                  |

The `IF` flag was also shown as set by GDB, but it is an interrupt-control flag and is not caused by the subtraction operation.

### sub1 Flag Summary

```text
CF = 1
PF = 1
AF = 0
ZF = 0
SF = 1
OF = 0
```

---

# 2. sub2.asm - 16-bit Subtraction

## Program Description

The program stores two 16-bit numbers:

```asm
num1 dw 1000
num2 dw 2000
```

It loads `num1` into the `AX` register and subtracts `num2`:

```asm
mov ax, [num1]
sub ax, [num2]
```

Therefore, the operation is:

```text
1000 - 2000 = -1000
```

Because `AX` is a 16-bit register, the negative result is represented using 16-bit two's complement.

The result is:

```text
-1000 = 1111 1100 0001 1000₂
      = FC18₁₆
```

GDB displays:

```text
EAX = 0xfc18 = 64536
```

The value `64536` is the unsigned interpretation of the 16-bit bit pattern `FC18`. When interpreted as a signed 16-bit value, `FC18` represents `-1000`.

## GDB Result

The subtraction instruction was located using:

```gdb
disassemble _start
```

The instruction immediately after the subtraction was:

```text
0x0804900d <+13>: mov %ax,0x804a004
```

A breakpoint was placed there so that the flags could be examined immediately after the subtraction.

The following commands were used:

```gdb
break *0x0804900d
run
info registers eax
p/x $eax
info registers eflags
```

The result was:

```text
eax      0xfc18    64536
$1       0xfc18
eflags   0x287     [ CF PF SF IF ]
```

## Flag Analysis

| Flag | Status  | Explanation                                                                                      |
| ---- | ------- | ------------------------------------------------------------------------------------------------ |
| CF   | Set     | 1000 is smaller than 2000 when treated as unsigned values, so the subtraction requires a borrow. |
| PF   | Set     | The low byte of `FC18` is `18`, or `00011000`. It contains two `1` bits, which is even.          |
| AF   | Cleared | The lower nibbles are `1000 - 0000`, so there is no borrow between bit 3 and bit 4.              |
| ZF   | Cleared | The result is not zero.                                                                          |
| SF   | Set     | The most significant bit of the 16-bit result is `1`, indicating a negative signed result.       |
| OF   | Cleared | `1000 - 2000 = -1000`, which is within the signed 16-bit range of `-32768` to `32767`.           |

The `IF` flag was also shown as set by GDB, but it is an interrupt-control flag and is not caused by the subtraction operation.

### sub2 Flag Summary

```text
CF = 1
PF = 1
AF = 0
ZF = 0
SF = 1
OF = 0
```

---

# Comparison of sub1 and sub2

| Feature            | sub1.asm | sub2.asm    |
| ------------------ | -------- | ----------- |
| Register used      | AL       | AX          |
| Operand size       | 8-bit    | 16-bit      |
| First number       | 50       | 1000        |
| Second number      | 80       | 2000        |
| Operation          | 50 - 80  | 1000 - 2000 |
| Signed result      | -30      | -1000       |
| Hex result         | E2       | FC18        |
| GDB unsigned value | 226      | 64536       |
| CF                 | Set      | Set         |
| PF                 | Set      | Set         |
| AF                 | Cleared  | Cleared     |
| ZF                 | Cleared  | Cleared     |
| SF                 | Set      | Set         |
| OF                 | Cleared  | Cleared     |

## Conclusion

The two programs demonstrate subtraction using different operand sizes.

`sub1.asm` performs an 8-bit subtraction:

```text
50 - 80 = -30
```

and produces the two's complement result:

```text
E2
```

`sub2.asm` performs a 16-bit subtraction:

```text
1000 - 2000 = -1000
```

and produces:

```text
FC18
```

In both cases, the Carry Flag is set because the unsigned subtraction requires a borrow. The Sign Flag is set because the most significant bit of each result is `1`. The Zero Flag is cleared because neither result is zero, while the Overflow Flag is cleared because both signed results are within their respective signed ranges.

The Parity Flag is set because the low byte of each result contains an even number of `1` bits. The Auxiliary Carry Flag is cleared because neither subtraction requires a borrow from bit 4.

These results were verified using GDB immediately after the subtraction instructions.
