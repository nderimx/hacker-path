# Hacking: the art of exploitation - *notes*

##### *"Thinking of C-source as a program is a common misconception that is expoited by hackers every day."*

## GDB - GNU Debugger
### Prep
#### First use the intel disassembly flavor
`$ echo "set dis intel" > ~/.gdbinit`
<br>The default at&t flavor is just clumsy
#### Debugging flags
* `gcc -g <source.c>` -> use -g in gcc to give gdb access to the source code
* `gdb -q ./a.out` -> use -q in gdb to use the extra information from the -g flag
* `(gdb) list` -> shows you all lines of the source code, so you can break at a specific line
### Commands
#### Examining memory
* `x/4cb 0x8048484` -> examine 4 bytes starting from 0x8048484 in ascii representation
* `x/s 0x8048484` -> examine string starting at 0x8048484 (stops when 0x0 is reached)
* `x/2xw 0x8048484` -> examine 2 words(2 or 4 bytes) in hex
* `x/8db 0x8048484` -> examine 8 bytes in decimal represantation
#### Backtracing the stack
* `bt` -> prints all functions (and local variables) that lead to the current function

## Exploit Protections
#### syscalls like strcpy() are at a slightly different address each time a program containing them is run
since Linux kernel 2.6.11

## General tools
#### CLI calculators
* `$ bc -ql 192*(256^3)+168*(256^2)+256+1`
* `$ pcalc 0y10111011` -> output: `187 0xBB 0y10111011`

## Memory Segmentation
### Segments
#### text (code)
*contains the cpu instructions*<br>
is read only to allow safely running it multiple times concurrently
#### data
*contains initialized global and static variables*<br>
these variables are able to persist across functions because they reside in their own memory segments (data & bss)
#### bss
*contains uninitialized global and static variables*
#### heap
*contains dynamic data buffers created with malloc()*<br>
This is where local and nonlocal array or struct pointers point to.<br>
The memory within the heap is managed by allocator and deallocator algorithms, which grow and shrink the heap<br>
Starts at low addresses and grows downward towards higher memory addresses
#### stack
*contains local variables and other stack frame components*<br> Starts at high memory addresses and grows upward towards lower memory addresses<br>
It's used to store context during function calls, and to recover the state before each function call.<br>
*The stack and heap grow towards each other in order to minimize wasted space*<br>
Each time a function is called a new stack frame is created.<br>
##### Stack Frame
* *\<Low addresses>* ...
* ----------end of stack frame----------
* local variables
* Saved Frame Pointer (SFP) - used to restore EBP
* Frame Pointer (EBP) - apparently redundant (to the EBP register); used to restore ESP
* Return address (ret) - used to restore EIP
* ----------beginning of stack frame----------
* Function parameters - for the above frame
* *... \<High addresses>*
##### Stack Pointers
* ESP -> top of stack (Stack Pointer)
* EBP -> base of top stack frame (Base Pointer)
##### Call instruction
0. before a call instruction, its parameters get moved to the stack (which make up the end of that frame)
<br>
<br>
1. pushes the return address to the stack (EIP+sizeof(instruction))
2. jumps to the beginning of the called function
#### CPU execution
When a program is executed the EIP is set to point to the first instruction of the code segment.
This is the Processor execution loop:
1. Read instruction EIP points to
2. Add byte length of read instruction to EIP
3. Execute read instruction; ^goto step 1.
