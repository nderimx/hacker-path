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
#### information revealers
* `$ which <program_name>` finds the binary location
* `$ id $(whoami)` returns the current user's id and its group ids
* `$ hexdump -C <file>` shows bytes alongside their string representations when applicable

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
This is where local and nonlocal array (created with allocators) or struct pointers point to.<br>
-- If (for example) `char var_name[size]` is used, the buffer will be allocated where the variable is located. In other words, not the heap. --<br>
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

## Programming
* `malloc()` allocates the given number rounded up so x%8==0 -> returns a void pointer (~uint) - null pointer is returned on error (0x0)
* `free()` deallocates the memory range starting the given pointer
* `void *err_malloc(unsigned int size) {...}` - in this case malloc goes through the error checking function, which *returns a void pointer*, which you don't see often
* simple error handling: `fprintf(stderr, "Error: could not..."); exit(-1);`
* proper error handling: `perror(error_message); exit(-1);` writes error_message to stderr and prints each error message in stderr in reverse order (colon seperated). So you can stack up errors and each time decide what to do (keep stacking, logging them, or exiting and printing them).
* #include <mylibrary> -> compiler looks for this file in standard include paths (example: /usr/include/mylibrary) | #include "mylibrary" -> compiler looks in the current directory
* memory of a struct is simply all its elements concatenated (without any padding probbably). the element's datatypes are stored in the struct itself (code segment).
* `srand(time(0));` sets a seed which is used by rand(), although rand() doesn't take arguments.
* `bzero(pointer, SIZE);` zeros out the given memory - useful when doing binary manipulations

## OS stuff
* file descriptors are unique numbers across open files, stored in a table in memory (but there is another table that stores the exact memory locations of every file and is stored in persistant memory), and are passed to other functions (like pointers to the beginning of the file memory)
* a program file with the setuid flag set, always executes with the owners privileges (could be root) (during the execution the current **effective user** is considered to be the owner) even if the user executing it (**real user**) is not the owner (given this user has execution permission). a setuid file should be always write protected and in some cases even read protected.
* the `chsh` program is a setuid program owned by root, which manages the /etc/passwd file. Depending on the real user executing it, chsh allows management of sections of this file.
* "a running program has both a real user ID and an effective user ID." - in C obtainable with `getuid()` and `geteuid()`
* `chmod u+s`

## Exploitation
##### Exploiting a program is simply a clever way of getting the computer to do what you want it to do, even if the currently running program was designed to prevent that action.
###### Most program exploits have to do with memory corruption
##### Security holes are flaws or oversights in the design of the program or the environment.
### When to look for Security holes
* when a program is quickly modified to expand its functionality
* when a service allows limited code execution/interpretation (sanbox environments, browsers,..)
### Types of Vulnerabilities
* off-by-one error (fencepost error) - for instance counting spaces instead of objects surrounding them
* injections (very often using unicode escape codes)
* buffer overflows
* format string vulnerabilities
* memory leaks

## Open questions and challenges
* why is the allocator reusing deallocated heapspace of 100 but not 50, when allocating 15(=>16)?
* In C, for the sake of performance, the programmer is responsible for data integrity, instead of the compiler, since it would do integrity checks on every variable (or maybe only on buffers, idk). Why is Rust almost on par with C when it comes to performance (according to the Rust team, I haven't tested it myself yet)?

<br><br>
*01.01.1970*