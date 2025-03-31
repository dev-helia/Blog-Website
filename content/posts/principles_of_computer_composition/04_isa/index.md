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

```

