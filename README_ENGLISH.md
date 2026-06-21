# 🚀 TWOTY 32-bit Virtual CPU & Architecture

[Jump to Russian Version / Перейти к русской версии](#russian-version)

---

## 🇺🇸 English Version

A custom 32-bit virtual processor built from scratch in C++. This architecture features its own Turing-complete Instruction Set Architecture (ISA), dynamic bit-width switching (8/16/32-bit) on-the-fly, hardware status flags, stack, RAM, and built-in support for text label pointers.

### 🛠 Hardware Specifications
* **Registers:** 8 general-purpose 32-bit registers (`R0` – `R7`).
* **RAM:** 1024 memory cells for arrays and text buffers.
* **Stack:** Hardware stack with 64 levels depth for functions (`CALL`/`RET`) and temporary data (`PUSH`/`POP`).
* **Status Flags:** 
  * `ZF` (Zero Flag) — set (`1`) if the result of the last arithmetic operation is `0`.
  * `SF` (Sign Flag) — set (`1`) if the result went negative (underflow).
* **Dynamic Bit-Width:** Software switching of computation masks to 8, 16, or 32 bits during code execution.

### 📜 Instruction Set Architecture (ISA)

| Opcode (Hex) | Instruction | Arguments | Description |
| :--- | :--- | :--- | :--- |
| **`01`** | `SET` | `[reg] [val]` | Load a value into a register. |
| **`02`** | `ADD` | `[dest] [src1] [src2]` | Addition: `dest = src1 + src2`. Modifies flags. |
| **`04`** | `SUB` | `[dest] [src1] [src2]` | Subtraction: `dest = src1 - src2`. Modifies flags. |
| **`05`** | `MUL` | `[dest] [src1] [src2]` | Multiplication: `dest = src1 * src2`. Modifies flags. |
| **`0F`** | `DIV` | `[dest] [src1] [src2]` | Division `src1 / src2` with hardware protection against division by zero. |
| **`1F`** | `MOD` | `[dest] [src1] [src2]` | Modulo remainder: `dest = src1 % src2`. Modifies flags. |
| **`25`** | `ADDI` | `[dest] [src] [val]` | Fast addition of a register with an immediate constant. |
| **`26`** | `SUBI` | `[dest] [src] [val]` | Fast subtraction of an immediate constant from a register. |
| **`06`** | `JUMP` | `[address/label]` | Unconditional jump to a memory address or text label. |
| **`07`** | `JZ` | `[address/label]` | Conditional jump: triggers if zero flag is set (`ZF == 1`). |
| **`23`** | `JNZ` | `[address/label]` | Conditional jump: triggers if zero flag is cleared (`ZF == 0`). |
| **`24`** | `JS` | `[address/label]` | Conditional jump: triggers if sign flag is set (`SF == 1`). |
| **`08`** | `LOOP` | `[reg_counter] [label]`| Decrements register by 1 and jumps to label if `reg != 0` (150ms delay). |
| **`0E`** | `CMP` | `[dest] [reg1] [reg2]` | Comparison. Writes to dest: `0` if equal, `1` if `reg1 > reg2`, `2` if `reg1 < reg2`. |
| **`10`** | `STORE` | `[addr_reg] [data_reg]`| Indirect write to RAM: address is taken from `addr_reg` register. |
| **`11`** | `LOAD` | `[dest_reg] [addr_reg]`| Indirect read from RAM: address is taken from `addr_reg` register. |
| **`21`** | `LD` | `[dest_reg] [addr]` | Direct read from RAM: address is specified as a hard number. |
| **`22`** | `ST` | `[addr] [src_reg]` | Direct write to RAM: address is specified as a hard number. |
| **`15`** | `PUSH` | `[reg]` | Push register value onto the top of the stack. |
| **`16`** | `POP` | `[reg]` | Pop value from the top of the stack and write to register. |
| **`17`** | `CALL` | `[label]` | Save return address to stack and jump to subroutine (function). |
| **`18`** | `RET` | *(none)* | Return from subroutine (pops address from stack to `pc`). |
| **`19`** | `AND` | `[dest] [src1] [src2]` | Bitwise logical AND. Modifies flags. |
| **`1A`** | `OR` | `[dest] [src1] [src2]` | Bitwise logical OR. Modifies flags. |
| **`1B`** | `XOR` | `[dest] [src1] [src2]` | Bitwise exclusive OR (Symmetric encryption). |
| **`1C`** | `SHL` | `[dest] [src] [shift_reg]`| Bitwise logical shift left (Fast multiplication by 2). |
| **`1D`** | `SHR` | `[dest] [src] [shift_reg]`| Bitwise logical shift right (Fast division by 2). |
| **`1E`** | `NOT` | `[dest] [src]` | Bitwise logical NOT (Inversion of all bits). |
| **`03`** | `PRINT` | `[reg]` | Output register value to console as an integer. |
| **`0A`** | `PRINT_CHAR` | `[reg]` | Output register value to console as an ASCII character. |
| **`0B`** | `CLEAR` | *(none)* | Full clear of terminal screen (`system("cls")`). |
| **`0C`** | `INPUT` | `[reg]` | Pause execution and read an integer from keyboard into register. |
| **`12`** | `INPUT_CHAR` | `[reg]` | Pause execution and read a single character (keypress) into register. |
| **`14`** | `INPUT_STR` | `[addr_reg]` | Read an entire text string from keyboard and map it to RAM cells. |
| **`13`** | `CMP_STR` | `[dest] [reg_adr1] [reg_adr2]`| Compare two strings in RAM by pointers. Returns `0` if they are identical. |
| **`0D`** | `RANDOM` | `[reg]` | Generate a random number from 1 to 100 based on system time. |
| **`20`** | `SET_MODE` | `[mode]` | Change computation bit-width in real-time (`0` = 8-bit, `1` = 16-bit, `2` = 32-bit). |
| **`FF`** | `HALT` | *(none)* | Full processor stop and program termination. |

### 🔨 Running Binary Code
```bash
emulator.exe program.bin
