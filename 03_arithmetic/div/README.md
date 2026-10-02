# Division Programs

This folder contains assembly programs demonstrating unsigned division using the x86 `DIV` instruction.

The programs tested were:

* `div1.asm` — 8-bit division
* `div2.asm` — 16-bit division

## div1.asm

### Operation

The program performs:

```text
100 ÷ 7
```

The relevant instructions are:

```asm
mov ax, [dividend]
mov bl, [divisor]
div bl
```

For an 8-bit `DIV`, the CPU uses `AX` as the dividend. The quotient is stored in `AL` and the remainder is stored in `AH`.

### GDB Result

After the `DIV` instruction:

```text
eax = 0x20e
```

This means:

```text
AX = 0x020E

AH = 0x02 = 2       ; remainder
AL = 0x0E = 14      ; quotient
```

Therefore:

```text
100 ÷ 7 = 14 remainder 2
```

The result can also be verified:

```text
14 × 7 + 2 = 100
```

### Flags

GDB displayed:

```text
eflags = 0x212 [ AF IF ]
```

The arithmetic flags after the `DIV` instruction should not be interpreted as describing the division result because `DIV` leaves CF, PF, AF, ZF, SF, and OF undefined.

| Flag | Status after DIV | Explanation                     |
| ---- | ---------------- | ------------------------------- |
| CF   | Undefined        | `DIV` does not define this flag |
| PF   | Undefined        | `DIV` does not define this flag |
| AF   | Undefined        | `DIV` does not define this flag |
| ZF   | Undefined        | `DIV` does not define this flag |
| SF   | Undefined        | `DIV` does not define this flag |
| OF   | Undefined        | `DIV` does not define this flag |

`IF` is unrelated to the arithmetic result.

---

## div2.asm

### Operation

The program performs:

```text
50000 ÷ 300
```

The relevant instructions are:

```asm
mov ax, [dividend]
mov dx, [highpart]
mov bx, [divisor]
div bx
```

For a 16-bit `DIV`, the dividend is stored in `DX:AX`. The quotient is stored in `AX` and the remainder is stored in `DX`.

In this program:

```text
DX = 0
AX = 50000
BX = 300
```

Therefore, the dividend is:

```text
DX:AX = 0:50000
```

### GDB Result

After the `DIV` instruction:

```text
eax = 0xa6
edx = 0xc8
ebx = 0x12c
```

Converting the hexadecimal values:

```text
AX = 0xA6 = 166
DX = 0xC8 = 200
BX = 0x12C = 300
```

Therefore:

```text
50000 ÷ 300 = 166 remainder 200
```

The result can be verified:

```text
166 × 300 + 200 = 50000
```

### Flags

GDB displayed:

```text
eflags = 0x212 [ AF IF ]
```

As with `div1`, the arithmetic flags should not be interpreted as describing the division result because `DIV` leaves CF, PF, AF, ZF, SF, and OF undefined.

| Flag | Status after DIV | Explanation                     |
| ---- | ---------------- | ------------------------------- |
| CF   | Undefined        | `DIV` does not define this flag |
| PF   | Undefined        | `DIV` does not define this flag |
| AF   | Undefined        | `DIV` does not define this flag |
| ZF   | Undefined        | `DIV` does not define this flag |
| SF   | Undefined        | `DIV` does not define this flag |
| OF   | Undefined        | `DIV` does not define this flag |

`IF` is unrelated to the arithmetic result.

---

## Comparison

| Program    | Dividend | Divisor | Quotient | Remainder |
| ---------- | -------: | ------: | -------: | --------: |
| `div1.asm` |      100 |       7 |       14 |         2 |
| `div2.asm` |    50000 |     300 |      166 |       200 |

The two programs demonstrate different operand sizes.

`div1.asm` uses 8-bit division:

```text
AX ÷ BL → AL = quotient, AH = remainder
```

`div2.asm` uses 16-bit division:

```text
DX:AX ÷ BX → AX = quotient, DX = remainder
```

## Conclusion

The division programs demonstrate how the x86 `DIV` instruction performs unsigned integer division and stores both the quotient and remainder.

`div1.asm` demonstrates 8-bit division, producing a quotient of 14 and a remainder of 2 from 100 divided by 7.

`div2.asm` demonstrates 16-bit division, producing a quotient of 166 and a remainder of 200 from 50000 divided by 300.

Unlike `ADD` and `SUB`, the arithmetic status flags are undefined after `DIV`. Therefore, the quotient and remainder registers are used to determine and verify the division result rather than relying on the arithmetic flags.
