# LOPA-8 CPU Architecture — v0.1

> Designed to be simple to emulate.

## Architecture

* 8-bit CPU architecture
* 16-bit address width
* Byte-addressable RAM
* Maximum RAM: 64 KB

### Registers

| Register      |   Size | Description                             |
| ------------- | -----: | --------------------------------------- |
| `A`, `B`, `C` |  8-bit | General-purpose registers               |
| `AC`          |  8-bit | Accumulator                             |
| `PC`          | 16-bit | PC = current opcode byte                |
| `PTR`         | 16-bit | Memory pointer                          |
| `FLAGS`       |  8-bit | `C` = Carry (bit 0), `Z` = Zero (bit 1) |
| `SP`          | 16-bit | Stack pointer                           |
| `RA`          | 16-bit | Return address                          |

## General Rules

* Every instruction takes up 16 bits.
* Instruction encoding:

  * 8-bit opcode
  * 4-bit register 1
  * 4-bit register 2
* Every value must be converted into binary by the assembler.
* The stack grows downwards.
* Reset state: every register = `0`, but `SP = 0xFFFF`.
* Little endian.
* No signed operations at the CPU level.
* The return address is stored in the register `RA`.

## Instruction Set

### Operands

* `X` and `Y` are always registers.
* If there is no `X` / `Y`, the value of that field is `0`.
* Register numbers are `1 - 8`.
* Immediates are converted into Register `C` by the assembler.

---

### `0` — `MOV X, Y`

Moves `Y` to `X`, where `X` and `Y` are registers.

---

### `1` — `MFM X`

Standing for **Move From Memory**.

Moves the byte `PTR` is pointing to into `X`.

---

### `2` — `MTM X`

Standing for **Move To Memory**.

Moves the byte `X` into where `PTR` is pointing to.

---

### `3` — `PUSH X`

Pushes `X` onto the stack.

Only registers can be pushed onto the stack.

---

### `4` — `POP X`

Pops from the stack.

Only registers can be restored from the stack.

---

### `5` — `ADD X, Y`

Adds register `Y` to register `X` and stores the result in `AC`.

---

### `6` — `SUB X, Y`

Subtracts register `Y` from register `X` and stores the result in `AC`.

---

### `7` — `DIV X, Y`

Divides register `Y` by register `X` and stores the result in `AC`.

---

### `8` — `MUL X, Y`

Multiplies register `Y` by register `X` and stores the result in `AC:A`.

---

### `9` — `SHL X, Y`

Shifts register `Y` left by register `X` and stores the result in `AC:A`.

---

### `10` — `SHR X, Y`

Shifts register `Y` right by register `X` and stores the result in `AC:A`.

---

### `11` — `AND X, Y`

Does a bitwise AND for `X` and `Y`.

---

### `12` — `OR X, Y`

Does a bitwise OR for `X` and `Y`.

---

### `13` — `XOR X, Y`

Does a bitwise XOR for `X` and `Y`.

---

### `14` — `NOT X, Y`

Does a bitwise NOT for `X` and `Y`.

---

### `15` — `JMP`

Jumps to `PTR`.

`RET` does not work here.

---

### `16` — `CMP X, Y`

Compares `X` and `Y`.

Sets the Zero flag if they are equal.

---

### `17` — `JZ`

Jumps to `PTR` if the Zero flag is set.

`RET` does not work here.

---

### `18` — `JC`

Jumps to `PTR` if the Carry flag is set.

`RET` does not work here.

---

# Assembler Pseudo-Instructions

## `CALL`

`CALL` does two things:

1. `RA = PC + 2`
2. Jumps to `PTR`

It is more like a pseudo-instruction that executes multiple instructions underneath. Creating this instruction is up to the assembler.

Because every instruction takes up 16 bits (2 bytes), `PC + 2` points to the next instruction when `PC` contains the current opcode byte.

## `RET`

`RET` is a pseudo-instruction.

It jumps to `RA`.

Creating this instruction is up to the assembler.
