🚀 TWOTY 8/16/32 bit Virtual CPU & Architecture

[Перейти к русской версии / Jump to Russian Version](README.md)

---

🇬🇧 English Version

A virtual 32-bit processor developed from scratch in C++. This architecture features
its own Turing-complete Instruction Set Architecture (ISA), dynamic bit-width
switching (8/16/32 bit) "on the fly," hardware status flags, a stack, Random Access
Memory (RAM), and built-in support for text label pointers.

🛠 Hardware Specifications
• Registers:  8 general-purpose 32-bit registers (R0 – R7).
• RAM:        1024 memory cells for arrays and text buffers.
• Stack:      Hardware stack with a depth of 64 levels for functions (CALL/RET)
               and temporary data (PUSH/POP).
• Status Flags:
    · ZF (Zero Flag) — set to 1 if the result of the last arithmetic operation is 0.
    · SF (Sign Flag) — set to 1 if the result underflowed into negative territory.
• Dynamic Bit-Width: Software-controlled switching of computation masks to 8, 16,
               or 32 bits directly during code execution.

📜 Instruction Set Architecture (ISA)

| Code (Hex) | Command      | Arguments                    | Description                                                    |
| :--------- | :----------- | :--------------------------- | :------------------------------------------------------------- |
| 01         | SET          | [reg] [val]                  | Write a numeric value into a register.                         |
| 02         | ADD          | [dest] [src1] [src2]         | Addition: dest = src1 + src2. Updates flags.                   |
| 04         | SUB          | [dest] [src1] [src2]         | Subtraction: dest = src1 - src2. Updates flags.                |
| 05         | MUL          | [dest] [src1] [src2]         | Multiplication: dest = src1 * src2. Updates flags.             |
| 0F         | DIV          | [dest] [src1] [src2]         | Division: src1 / src2 with hardware protection against         |
|            |              |                              | division by zero.                                              |
| 1F         | MOD          | [dest] [src1] [src2]         | Modulo (remainder): dest = src1 % src2. Updates flags.         |
| 25         | ADDI         | [dest] [src] [val]           | Fast addition of a register with a raw constant.               |
| 26         | SUBI         | [dest] [src] [val]           | Fast subtraction of a raw constant from a register.            |
| 06         | JUMP         | [address/label]              | Unconditional jump to a memory address or text label.          |
| 07         | JZ           | [address/label]              | Conditional jump: triggers if the zero flag is set (ZF == 1).  |
| 23         | JNZ          | [address/label]              | Conditional jump: triggers if the zero flag is cleared         |
|            |              |                              | (ZF == 0).                                                     |
| 24         | JS           | [address/label]              | Conditional jump: triggers if the sign flag is set (SF == 1).  |
| 08         | LOOP         | [reg_counter] [label]        | Decrements the register by 1 and jumps to the label if         |
|            |              |                              | reg != 0 (150ms delay).                                        |
| 0E         | CMP          | [dest] [reg1] [reg2]         | Comparison. Writes to dest: 0 if equal, 1 if reg1 > reg2,      |
|            |              |                              | 2 if reg1 < reg2.                                              |
| 10         | STORE        | [addr_reg] [data_reg]        | Indirect write to RAM: address is taken from addr_reg.         |
| 11         | LOAD         | [dest_reg] [addr_reg]        | Indirect read from RAM: address is taken from addr_reg.        |
| 21         | LD           | [dest_reg] [addr]            | Direct read from RAM: address is specified as a hard number.   |
| 22         | ST           | [addr] [src_reg]             | Direct write to RAM: address is specified as a hard number.    |
| 15         | PUSH         | [reg]                        | Push a register value onto the top of the stack.               |
| 16         | POP          | [reg]                        | Pop a value from the top of the stack into a register.         |
| 17         | CALL         | [label]                      | Save the return address onto the stack and jump to a           |
|            |              |                              | subroutine (function).                                         |
| 18         | RET          | (none)                       | Return from a subroutine (pops the address from the stack      |
|            |              |                              | into pc).                                                      |
| 19         | AND          | [dest] [src1] [src2]         | Bitwise logical AND. Updates flags.                            |
| 1A         | OR           | [dest] [src1] [src2]         | Bitwise logical OR. Updates flags.                             |
| 1B         | XOR          | [dest] [src1] [src2]         | Bitwise exclusive OR (Symmetric encryption).                   |
| 1C         | SHL          | [dest] [src] [shift_reg]     | Bitwise logical shift left (Fast multiplication by 2).         |
| 1D         | SHR          | [dest] [src] [shift_reg]     | Bitwise logical shift right (Fast division by 2).              |
| 1E         | NOT          | [dest] [src]                 | Bitwise logical NOT (Inversion of all bits).                   |
| 03         | PRINT        | [reg]                        | Output a register value to the console as an integer.          |
| 0A         | PRINT_CHAR   | [reg]                        | Output a register value to the console as an ASCII character.  |
| 0B         | CLEAR        | (none)                       | Full terminal screen clear (system("cls")).                    |
| 0C         | INPUT        | [reg]                        | Pause execution and read an integer from the keyboard into a   |
|            |              |                              | register.                                                      |
| 12         | INPUT_CHAR   | [reg]                        | Pause execution and read a single character (keypress) into a  |
|            |              |                              | register.                                                      |
| 14         | INPUT_STR    | [addr_reg]                   | Read an entire text string from the keyboard and store it      |
|            |              |                              | character by character across RAM cells.                       |
| 13         | CMP_STR      | [dest] [reg_adr1] [reg_adr2] | Compare two strings in RAM by pointers. Returns 0 if they are  |
|            |              |                              | identical.                                                     |
| 0D         | RANDOM       | [reg]                        | Generate a random number from 1 to 100 based on system time.   |
| 20         | SET_MODE     | [mode]                       | Switch computation bit-width in real time (0 = 8-bit,          |
|            |              |                              | 1 = 16-bit, 2 = 32-bit).                                       |
| FF         | HALT         | (none)                       | Full processor halt and program termination.                   |

🔨 Running Binary Code
> emulator.exe program.bin
