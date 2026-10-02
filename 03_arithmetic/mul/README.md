# Multiplication Programs

This folder contains two x86 assembly language programs that demonstrate unsigned multiplication using 8-bit and 16-bit operands.

## Programs

* `mul1.asm` - 8-bit multiplication
* `mul2.asm` - 16-bit multiplication

Both programs were assembled using NASM and linked as 32-bit Linux executables. GDB was used to inspect the multiplication results and CPU flags immediately after the `MUL` instruction.

---

# 1. mul1.asm - 8-bit Multiplication

## Program Description

The program stores two 8-bit numbers:

```asm
num1 db 25
num2 db 10
```

The first number is loaded into the `AL` register:

```asm
mov al, [num1]
```

The `MUL` instruction then multiplies `AL` by the second operand:

```asm
mul byte [num2]
```

For an 8-bit `MUL`, the result is stored in the 16-bit `AX` register.

Therefore:

```text
25 × 10 = 250
```

The result in hexadecimal is:

```text
250 = 00FA₁₆
```

Therefore:

```text
AX = 00FA
```

## GDB Result

The multiplication instruction was located using:

```gdb
disassemble _start
```

The relevant instructions were:

```text
0x08049005 <+5>:     mulb   0x804a001
0x0804900b <+11>:    mov    %ax,0x804a002
```

A breakpoint was placed at `0x0804900b` so that the registers and flags could be inspected immediately after `MUL`.

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
eax      0xfa    250
$1       0xfa
eflags   0x202   [ IF ]
```

The lower 16 bits of `EAX` contain:

```text
AX = 00FA
```

which represents 250.

## Flag Analysis

For the `MUL` instruction, the Carry Flag and Overflow Flag are defined based on whether the upper half of the result is zero.

The other arithmetic flags are undefined after `MUL`.

| Flag | Status    | Explanation                                                                                     |
| ---- | --------- | ----------------------------------------------------------------------------------------------- |
| CF   | Cleared   | The upper half of the 16-bit result is zero. The result `00FA` fits entirely in the lower half. |
| OF   | Cleared   | The upper half of the result is zero, so no overflow occurred for the multiplication.           |
| PF   | Undefined | `MUL` does not define the Parity Flag.                                                          |
| AF   | Undefined | `MUL` does not define the Auxiliary Carry Flag.                                                 |
| ZF   | Undefined | `MUL` does not define the Zero Flag.                                                            |
| SF   | Undefined | `MUL` does not define the Sign Flag.                                                            |

GDB also showed the `IF` flag as set. `IF` controls interrupts and is not an arithmetic result flag.

### mul1 Flag Summary

```text
CF = 0
OF = 0
PF = Undefined
AF = Undefined
ZF = Undefined
SF = Undefined
```

---

# 2. mul2.asm - 16-bit Multiplication

## Program Description

The program stores two 16-bit numbers:

```asm
num1 dw 3000
num2 dw 200
```

The first number is loaded into `AX`:

```asm
mov ax, [num1]
```

The program then performs a 16-bit multiplication:

```asm
mul word [num2]
```

For a 16-bit `MUL`, the result is stored in the combined `DX:AX` registers.

Therefore:

```text
3000 × 200 = 600000
```

The result in hexadecimal is:

```text
600000 = 000927C0₁₆
```

The result is divided between the registers:

```text
DX = 0009
AX = 27C0
```

Therefore:

```text
DX:AX = 0009:27C0
```

Combining these two registers gives:

```text
000927C0₁₆ = 600000
```

## GDB Result

The multiplication instruction was located using:

```gdb
disassemble _start
```

The relevant instructions were:

```text
0x08049006 <+6>:     mulw   0x804a002
0x0804900d <+13>:    mov    %ax,0x804a004
```

A breakpoint was placed at `0x0804900d` so that the registers and flags could be inspected immediately after `MUL`.

The following commands were used:

```gdb
break *0x0804900d
run
info registers eax edx
p/x $eax
p/x $edx
info registers eflags
```

The result was:

```text
eax      0x27c0    10176
edx      0x9       9
$1       0x27c0
$2       0x9
eflags   0xa03     [ CF IF OF ]
```

Therefore:

```text
AX = 27C0
DX = 0009
DX:AX = 0009:27C0
```

This represents:

```text
600000
```

## Flag Analysis

| Flag | Status    | Explanation                                                                                                             |
| ---- | --------- | ----------------------------------------------------------------------------------------------------------------------- |
| CF   | Set       | The upper half of the result in `DX` is non-zero (`0009`), so the result does not fit entirely in 16 bits.              |
| OF   | Set       | The upper half of the result is non-zero, so the multiplication caused an overflow relative to the lower 16-bit result. |
| PF   | Undefined | `MUL` does not define the Parity Flag.                                                                                  |
| AF   | Undefined | `MUL` does not define the Auxiliary Carry Flag.                                                                         |
| ZF   | Undefined | `MUL` does not define the Zero Flag.                                                                                    |
| SF   | Undefined | `MUL` does not define the Sign Flag.                                                                                    |

GDB also showed the `IF` flag as set. This is an interrupt-control flag and is unrelated to the multiplication result.

### mul2 Flag Summary

```text
CF = 1
OF = 1
PF = Undefined
AF = Undefined
ZF = Undefined
SF = Undefined
```

---

# Comparison of mul1 and mul2

| Feature          | mul1.asm  | mul2.asm    |
| ---------------- | --------- | ----------- |
| Operand size     | 8-bit     | 16-bit      |
| First number     | 25        | 3000        |
| Second number    | 10        | 200         |
| Operation        | 25 × 10   | 3000 × 200  |
| Result           | 250       | 600000      |
| Result registers | AX        | DX:AX       |
| Hex result       | `00FA`    | `0009:27C0` |
| CF               | Cleared   | Set         |
| OF               | Cleared   | Set         |
| PF               | Undefined | Undefined   |
| AF               | Undefined | Undefined   |
| ZF               | Undefined | Undefined   |
| SF               | Undefined | Undefined   |

## Conclusion

The two programs demonstrate how the x86 `MUL` instruction handles different operand sizes.

`mul1.asm` performs an 8-bit multiplication:

```text
25 × 10 = 250
```

The result is stored in `AX` as:

```text
00FA
```

The upper half of the result is zero, so both CF and OF are cleared.

`mul2.asm` performs a 16-bit multiplication:

```text
3000 × 200 = 600000
```

The result is larger than 16 bits, so the CPU stores the 32-bit result across `DX:AX`:

```text
DX:AX = 0009:27C0
```

Because the upper half (`DX`) is non-zero, both CF and OF are set.

Unlike arithmetic instructions such as `ADD` and `SUB`, the `MUL` instruction does not define PF, AF, ZF, or SF. Therefore, these flags should be documented as undefined rather than interpreting their absence from GDB output as cleared.

The results and flags were verified using GDB immediately after the multiplication instructions.
