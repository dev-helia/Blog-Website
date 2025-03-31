---
title: "Instruction Set Architecture"
date: 2025-03-31
weight: 4
tags: ["ISA", "Instruction Format", "RISC-V"]
---
# Instruction Formats Overview
## 1. What is an Instruction Format?

In most mainstream instruction set architectures (ISAs), an instruction is parsed from **right to left** in execution.

For example, after an instruction like:

```
ecall
```

It simply ends execution. But most other instructions contain:

- `rd`: destination register
- `rs1`, `rs2`: source registers

In **RISC-V**, instruction format **strictly requires** `rs1` and `rs2` to be placed at fixed bit positions in the 32-bit instruction word.  
> This avoids decoding complexity caused by variable field placement.

---

## 2. The 6 RISC-V Instruction Formats

| Format     | Description                                  | Examples        |
|------------|----------------------------------------------|-----------------|
| R-Format   | Uses three register inputs                   | `add`, `xor`, `mul` (arithmetic/logical) |
| I-Format   | Contains immediate value, 1 source register  | `addi`, `lw`, `jalr`, `slli` |
| S-Format   | Store-type instructions                      | `sw`, `sb` |
| SB-Format  | Branch instructions                          | `beq`, `bge` |
| U-Format   | Upper immediate (20-bit) instructions        | `lui`, `auipc` |
| UJ-Format  | Jump-type instructions                       | `jal` |

---

## 3. Bit-Level Structure of Formats

All RISC-V instructions are 32 bits wide.  
The bit fields are assigned based on the instruction type. Below is a standard breakdown:

```
R-Type:   [ funct7 | rs2 | rs1 | funct3 | rd | opcode ]
I-Type:   [ imm[11:0] | rs1 | funct3 | rd | opcode ]
S-Type:   [ imm[11:5] | rs2 | rs1 | funct3 | imm[4:0] | opcode ]
SB-Type:  [ imm[12], imm[10:5], rs2, rs1, funct3, imm[4:1], imm[11], opcode ]
U-Type:   [ imm[31:12] | rd | opcode ]
UJ-Type:  [ imm[20], imm[10:1], imm[11], imm[19:12], rd, opcode ]
```

> Each format encodes information differently based on what is needed for that instruction.

---

## 4. Why So Many Formats?

Each instruction type serves different purposes and needs different fields:

- R-Type: needs 3 registers → `rs1`, `rs2`, `rd`
- I-Type: needs only one register + immediate → `rs1`, `imm`
- S-Type: uses split immediate → `imm[11:5]`, `imm[4:0]`
- SB-Type: uses **branch offset** as an immediate → needs bit-rearranged `imm`
- U/UJ-Types: use large constants or absolute addresses (e.g., for jump or base)

Each format ensures fixed decoding paths and consistent field positioning.

# ISC-V Assembly Examples
## 1. Example: Load Array Elements and Constants

```asm
.section .data
arr: .word 2, 4, 6, 8        # Define an array
n:   .word 9                 # Define an integer variable 'n'

.section .text
.globl _start
_start:

    # Load the base address of arr into register a0
    la a0, arr               # a0 = base address of arr

    # Load array elements into a1–a4
    lw a1, 0(a0)             # a1 = arr[0] = 2
    lw a2, 4(a0)             # a2 = arr[1] = 4
    lw a3, 8(a0)             # a3 = arr[2] = 6
    lw a4, 12(a0)            # a4 = arr[3] = 8

    # Load the value of variable n
    la a5, n                 # a5 = address of n
    lw a6, 0(a5)             # a6 = value of n = 9

    # Exit the program using ecall (syscall 10)
    li a7, 10                # syscall code 10 = exit
    ecall
```

This demonstrates how `.data` and `.text` sections are used in RISC-V, and how addresses and values are handled using `la` (load address) and `lw` (load word).

---

## 2. Example: Fibonacci Sequence in Assembly

Below is a RISC-V assembly implementation of the Fibonacci sequence.

```asm
# Fibonacci Sequence
main:   
    add   t0, x0, x0         # t0 = 0
    addi  t1, x0, 1          # t1 = 1
    la    t3, n              # t3 = address of n
    lw    t3, 0(t3)          # t3 = value of n

fib:
    beq   t3, x0, finish     # if n == 0, jump to finish
    add   t2, t0, t1         # t2 = t0 + t1
    mv    t0, t1             # move t1 → t0
    mv    t1, t2             # move t2 → t1
    addi  t3, t3, -1         # n = n - 1
    j     fib                # jump to next iteration

finish:
    addi  a0, t0, 0          # prepare return value
    li    a7, 1              # syscall code 1 = print integer
    ecall                    # print result
    li    a7, 10             # syscall code 10 = exit
    ecall
```

### Notes:
- `addi`: add immediate value  
- `mv`: pseudo-instruction for `addi x, y, 0`  
- `beq`: branch if equal  
- `j`: unconditional jump (pseudoinstruction for `jal x0, label`)  
- `la`: load address (pseudo-instruction expanded to `auipc` + `addi`)

This example shows:
- Register usage
- Looping
- Branching
- System calls via `ecall`

---

This block clearly illustrates how a real algorithm like Fibonacci can be implemented directly in RISC-V instructions, showing you the flow of control and data step-by-step.


# Shift Instructions & Formats
## 1. Shift Instructions

### 🧭 Instruction Format Summary

| Instruction | Meaning                          |
|-------------|----------------------------------|
| `sll rd, rs1, rs2`   | Shift Left Logical: rd = rs1 << rs2 |
| `slli rd, rs1, imm`  | Shift Left Logical with immediate |
| `srl rd, rs1, rs2`   | Shift Right Logical: rd = rs1 >> rs2 |
| `srli rd, rs1, imm`  | Shift Right Logical with immediate |
| `sra rd, rs1, rs2`   | Shift Right Arithmetic (sign-preserving) |
| `srai rd, rs1, imm`  | Shift Right Arithmetic with immediate |

These instructions shift the bits in `rs1` and store the result in `rd`.

---

## 2. R-Format Instruction Layout

An R-type instruction is 32 bits wide and consists of the following fields:

| Field     | Width (bits) | Description           |
|-----------|---------------|-----------------------|
| `funct7`  | 7             | Operation modifier    |
| `rs2`     | 5             | Second source register|
| `rs1`     | 5             | First source register |
| `funct3`  | 3             | Operation selector    |
| `rd`      | 5             | Destination register  |
| `opcode`  | 7             | Operation category    |

```plaintext
31      25 24   20 19   15 14  12 11   7 6    0
[funct7][ rs2 ][ rs1 ][f3 ][ rd ][opcode]
```

Note:
- All fields are **unsigned integers**
- 5-bit fields represent values 0–31 (suitable for register numbers)
- `funct3` and `funct7` differentiate between similar instructions

---

## 3. I-Format Instruction Layout (for immediates)

When an instruction has an immediate (like `slli` or `srli`), it uses I-type format.

| Field     | Width (bits) | Description           |
|-----------|---------------|-----------------------|
| `imm[11:0]` | 12           | Immediate value       |
| `rs1`     | 5             | Source register       |
| `funct3`  | 3             | Operation selector    |
| `rd`      | 5             | Destination register  |
| `opcode`  | 7             | Operation category    |

```plaintext
31        20 19   15 14  12 11   7 6    0
[ imm[11:0] ][ rs1 ][ f3 ][ rd ][opcode]
```

### Why I-format?
- 12 bits for the immediate field allow larger constants
- Only one source register (rs1) and one destination register (rd)
- Preserves simplicity for hardware decoder

---

This section gives a compact overview of how **bitwise shift operations** are encoded and how **R/I format structures** differ.

# Data Transfer Instructions
## 2. Data Transfer Instructions

### 🧭 Format: `memop reg, offset(base_reg)`

```plaintext
memop   = lw / sw / lb / sb / lh / sh / lbu / lhu
reg     = target register (for load) or source register (for store)
offset  = immediate value (in bytes)
base_reg = register holding the base address
```

The effective memory address is computed as:

```
effective_address = base_reg + offset
```

---

## 3. Load Instructions

| Instruction   | Meaning                                      |
|---------------|----------------------------------------------|
| `lw rd, offset(rs1)`   | Load 32-bit word from memory into `rd`         |
| `lb rd, offset(rs1)`   | Load 8-bit signed byte from memory into `rd`  |
| `lbu rd, offset(rs1)`  | Load 8-bit **unsigned** byte into `rd`        |
| `lh rd, offset(rs1)`   | Load 16-bit signed half-word                  |
| `lhu rd, offset(rs1)`  | Load 16-bit unsigned half-word                |

---

## 4. Store Instructions

| Instruction   | Meaning                                         |
|---------------|-------------------------------------------------|
| `sw rs2, offset(rs1)`  | Store 32-bit word from `rs2` into memory        |
| `sb rs2, offset(rs1)`  | Store lower 8 bits of `rs2` into memory         |
| `sh rs2, offset(rs1)`  | Store lower 16 bits of `rs2` into memory        |

Note: All load/store addresses must be **aligned** if required by platform (e.g. 32-bit aligned for `lw/sw`)

---

## 5. Register vs Memory

- Memory accesses are **slower** than registers
- Use registers (`a0–a7` for arguments, `a0–a1` for return values) when possible
- Stack memory is managed using `sp` (stack pointer)

---

## 6. Example (Assembly)

```assembly
.data
var: .word 42

.text
.globl _start
_start:
    la a0, var        # Load address of var
    lw a1, 0(a0)      # Load 32-bit value from var to a1 (should be 42)
    li a2, 100        # Set a2 to 100
    sw a2, 0(a0)      # Store 100 back to var
```

---

## 7. Summary

- `lw/sw`: 32-bit word
- `lh/sh`: 16-bit half-word
- `lb/sb`: 8-bit byte
- `lbu/lhu`: load unsigned variant (no sign-extension)

Choosing correct instruction ensures proper **sign-extension** and **data width** when transferring between memory and registers.


# 3. Branching & Jump Instructions

## 1 Conditional Branches (SB-Format)

| Instruction         | Meaning                           |
|---------------------|-----------------------------------|
| `beq rs1, rs2, label` | Branch if Equal                   |
| `bne rs1, rs2, label` | Branch if Not Equal               |
| `blt rs1, rs2, label` | Branch if Less Than               |
| `bge rs1, rs2, label` | Branch if Greater or Equal        |

These instructions compare `rs1` and `rs2`, and **jump to the label** if the condition is met.

SB-Format encodes the target label using a **13-bit signed offset** (from current PC). Offset is always **2-byte aligned**, so encoded as offset / 2.

---

## 2 Jump Instructions (UJ-Format & JAL)

| Instruction    | Purpose                                 |
|----------------|------------------------------------------|
| `jal rd, label`   | Jump and Link: set `rd = PC + 4`, then jump to label |
| `jalr rd, rs1, imm` | Jump and Link Register: `PC = rs1 + imm`, `rd = PC + 4` |

The **link** part means saving the return address (i.e., where to come back) in `rd`.

Common pattern in function calls:

```assembly
jal ra, func     # Call function (store return address in ra)
...
ret              # Return (actually `jalr x0, ra, 0`)
```

---

## 3 Instruction Formats

### SB-Format (Branch)

```text
imm[12] | imm[10:5] | rs2 | rs1 | funct3 | imm[4:1] | imm[11] | opcode
```

- The 13-bit immediate is split across multiple fields.
- Must multiply by 2 to get actual byte offset.

### UJ-Format (jal)

```text
imm[20] | imm[10:1] | imm[11] | imm[19:12] | rd | opcode
```

- Immediate total 21 bits, signed.
- Also needs shift left by 1 (since all jumps are 2-byte aligned)

### I-Format (jalr)

```text
imm[11:0] | rs1 | funct3 | rd | opcode
```

- Same format as load instructions.
- Used in `jalr` for indirect jump (like function returns)

---

## 4 Assembly Example

```assembly
.globl _start
_start:
    li t0, 0         # sum = 0
    li t1, 10        # loop counter

loop:
    add a0, a0, t0   # do something
    addi t0, t0, 1
    blt t0, t1, loop # repeat if t0 < t1

    li a7, 10
    ecall
```

---

## 5 Summary

- `beq`, `bne`, `blt`, `bge` are for condition-based control flow
- `jal`, `jalr` are used for function call/return
- Always use `jalr` for **function returns**
- Understand immediate encoding and format is crucial for ISA decoding

# Bitwise Operation Instructions
Bitwise operations are commonly used for **flag manipulation**, **masking**, and **low-level data manipulation**.

## 1 Register-to-Register Operations

| Instruction        | Description         |
|--------------------|---------------------|
| `and rd, rs1, rs2` | Bitwise AND         |
| `or rd, rs1, rs2`  | Bitwise OR          |
| `xor rd, rs1, rs2` | Bitwise XOR         |

These operations take two source registers and apply bitwise logic, storing the result in `rd`.

---

## 2 Register-Immediate Operations

| Instruction         | Description                   |
|---------------------|-------------------------------|
| `andi rd, rs1, imm` | Bitwise AND with immediate    |
| `ori rd, rs1, imm`  | Bitwise OR with immediate     |
| `xori rd, rs1, imm` | Bitwise XOR with immediate    |

Immediate is usually used for **masking specific bits**, such as extracting the lower byte.

---

## 3 Example: Masking Lower 8 Bits

```assembly
li t0, 0x1234        # Load a 16-bit value
andi t1, t0, 0xFF    # Mask and keep only the lower 8 bits
# t1 = 0x34
```

This is a typical **bitmask operation**, often used in:
- Extracting specific byte from word
- Checking flag bits
- Performing low-level I/O operations

---

## 4 Summary

- `and`, `or`, `xor` operate on two registers  
- `andi`, `ori`, `xori` combine register with constant  
- Commonly used with masks like `0xFF`, `0xF0`, `0x0F`  
- No sign extension: bitwise logic applies **bit-by-bit**


# Arithmetic Instructions

---

Arithmetic operations include basic add/subtract, multiplication/division, and immediate operations.

## 1 Register-based Arithmetic

| Instruction        | Description     |
|--------------------|-----------------|
| `add rd, rs1, rs2` | Addition        |
| `sub rd, rs1, rs2` | Subtraction     |
| `mul rd, rs1, rs2` | Multiplication  |
| `div rd, rs1, rs2` | Division        |

These use two register operands and return the result in `rd`.

---

## 2 Immediate Arithmetic

| Instruction           | Description              |
|------------------------|--------------------------|
| `addi rd, rs1, imm`    | Add with constant value  |

Immediate must fit in **12-bit signed integer**. Larger constants need to use `lui`.

---

## 3 Example: Basic Arithmetic

```assembly
li t1, 10
li t2, 3

add t3, t1, t2   # t3 = 10 + 3
sub t4, t1, t2   # t4 = 10 - 3
mul t5, t1, t2   # t5 = 10 * 3
div t6, t1, t2   # t6 = 10 / 3
```

---

# U-Type Format: Dealing with Large Immediates

---

Sometimes, constants > 12 bits are needed. I-Type only allows 12 bits.

To handle this, RISC-V defines **U-Type** (Upper Immediate) format.

## 1 Instruction Format

```
|  imm[31:12]  |   rd   | opcode |
|   20 bits    | 5 bits | 7 bits |
```

## 2 Supported Instructions

| Instruction | Description                   |
|-------------|-------------------------------|
| `lui`       | Load Upper Immediate           |
| `auipc`     | Add Upper Immediate to PC      |

They place a 20-bit value into the upper 20 bits of a register (`imm << 12`), which can then be combined with `addi`.

---

## 3 Example

```assembly
lui t0, 0x12345       # t0 = 0x12345000
addi t0, t0, 0x67     # t0 = 0x12345067 (final constant)
```

This two-step process is how RISC-V constructs **full 32-bit constants**.

# Special Instruction

---

### `nop`: No Operation

- `nop` stands for "no operation".  
- When executed, it performs **no effect** on the program state.

### Implementation Detail

In RISC-V, `nop` is a **pseudo-instruction**:
```assembly
nop     # Equivalent to: addi x0, x0, 0
```
It uses register x0 (always 0), so it does nothing.

---

### Common Use Cases

| Scenario            | Description                                                  |
|---------------------|--------------------------------------------------------------|
| Pipeline Padding     | Inserted to avoid hazards (especially in pipelined CPUs)     |
| Instruction Alignment| Used to align instructions on memory boundaries              |
| Debugging/Breakpoints| As placeholders when modifying instruction flow              |
| Delay Slots (legacy) | In older architectures, sometimes used to fill delay slots   |

`nop` is simple but often essential in low-level optimization and hardware debugging contexts.
```
