+++ 
draft = false
date = 2022-05-08T21:18:20+06:30
title = "Memory Layout of C program"
authors = 'Thwin Htet Win'
categories = ['C','Binary Exploitation']
externalLink = ""
series = []
+++

# Memory Layout of C program

Created: June 7, 2021 7:49 PM

I think you guys are already familiar with the fundamentals of C programming.

Most of us already learnt what is stack, heap, memory but university was all text books.

Now,let's study about memory layout of C program.

Although we are not going to study about Assembly, we will introduce some Assembly Instructions and debugging to understand deeply.

Don't worry.just a few things.You will get it!

Before we begin, you should familiar with Pointer, dereference and address-of operators.

## Pointer, Dereference and address-of Operators

### Pointer

Pointer is defined by prepending an asterisk (*) to a variable name.

Pointer points to memory address of its referenced variable.

### Address-of operator

*address-of* operator is defined by  prepending an ampersand (&)  to a variable name.

*address-of* operator is used in conjunction with pointer.

a*ddress-of* operator contains memory address of that variable.

### Dereference operator

*dereference* operator is defined by prepending an asterisk (*) to a variable name.

*dereference* will return the data found in the address the pointer is pointing to.

[ pointer* will return memory address,while dereference* will return the data found in that address ]

Study the following code and output.

If you understand the program, write a similar one to ensure your knowledge.

```c
//ptr-addr-deref.c

#include <stdlib.h>
#include <stdio.h>

int main()
{
	int int_var;
	int *int_ptr;

	int_var = 1337;
	int_ptr = &int_var;

	printf("int_var is located at %p and contains %d\n",&int_var,int_var);
	printf("int_ptr is located at %p , contains %p and points to %d\n",&int_ptr,int_ptr,*int_ptr); // here *int_ptr is dereference.
}
```

```bash
root@zus:Desktop/memory_layout # ./ptr-addr-deref 
int_var is located at 0xff97758c and contains 1337
int_ptr is located at 0xff977588 , contains 0xff97758c and points to 1337
```

## The Memory

When a program is executed,the Operating System creates address space for that program and maps the elements of program into memory.

Then compiled program's segments are loaded into that newly created address space.

Everything about program's requirements are included in that address space.

Now, we need to know a little bit about how a program's memory is laid out.

```bash
Low memory addresses (0x08000000) :
                                        ------------------------
                                        |    text segment      |
                                        ------------------------
                                        |    data segment      |
                                        ------------------------
                                        |    bss segment       |
                                        ------------------------
                                        |    heap segment      | ↓ heap grows downward (to higher memory addresses)
                                        ------------------------
                                        |    unused memory     |
                                        ------------------------
                                        |    stack segment     | ↑ stack grows upward (to lower memory addresses)
                                        ------------------------

High memory addresses (0xbfffffff) :
```

## Memory Segmentation

The compiled program's memory is divided into 5 segments.

1. .text
2. .data
3. .bss
4. .heap
5. .stack

### text segment

.*text* segment is used to locate assembly instructions.

.*text* segment is read-only segment.

.*text* segment is fixed-size segment.

### data segment

.*data* segment is used to store initialized global variables and initialized static variables.

.*data* segment is writable segment.

.*data* segment is fixed-size segment.

### bss segment

.*bss* segment is used to store uninitialized global variables and uninitialized static variables.

.*bss* segment is writable segment.

.*bss* segment is fixed-size segment.

### heap segment

The heap is *First-In-First-Out* (FIFO) data structure.

.*heap* segment can be directly controlled by a programmer.

.*heap* segment is not fixed-size segment.

.*heap* segment grows downward (to higher memory addresses).

### stack segment

The stack is *Last-In-First-Out* (LIFO) also called *First-In-Last-Out* (FILO) data structure.

.*stack* segment is used to store local variables,function arguments and so on.

.*stack* segment is not fixed-size segment.

---

Study the following code and take a look at output memory addresses.

```c
//memory_segments.c
//demo of memory segments
Memory segments
#include <stdio.h>
#include <stdlib.h>

int global_var; //this variable will store in bss segment because this is a global variable. 
int global_initialized_var = 1337; //this variable will store in data segment because this is a global initialized variable. 

void test_function()
{
	int stack_var; //this variable will store in stack because this is a local function.
	printf("[.stack segment]-----------------------------\n\n");

	printf("test_function's stack_var is located at %p\n",&stack_var);
	printf("\n");
}

int main()
{
	int stack_var; //this variable will store in stack because this is a local function.
	static int static_var; //this variable will store in bss segment because this is a static variable. 
	static int static_initialized_var = 100; //this variable will store in data segment because this is a static initialized variable. 

	int *heap_var_ptr;
	heap_var_ptr = (int *)malloc(4);
	//remember that we can control the heap segment and allocate?

	printf("\n[.data segment]------------------------------\n\n");
	printf("global_initialized_var is at %p\n",&global_initialized_var);
	printf("static_initialized_var is at %p\n",&static_initialized_var);

	printf("\n");

	printf("[.bss segment]-------------------------------\n\n");
	printf("global_var is at %p\n",&global_var);
	printf("static_var is at %p\n",&static_var);

	printf("\n");

	printf("[.heap segment]------------------------------\n\n");
	printf("heap_var is at %p\n",heap_var_ptr);

	printf("\n");

	test_function();

}
```

```bash
root@zus:Desktop/memory_layout # ./memory_segments              

[.data segment]------------------------------

global_initialized_var is at 0x5659f028
static_initialized_var is at 0x5659f02c

[.bss segment]-------------------------------

global_var is at 0x5659f034
static_var is at 0x5659f038

[.heap segment]------------------------------

heap_var is at 0x56fa61a0

[.stack segment]-----------------------------

test_function's stack_var is located at 0xff967e9c
```

As we learnt,lower memory addresses are located at the higher places.

Check the output's content.

Variables inside .*data* segment have the lower addresses than the variables' addresses which are located within other segments.

Now, you understand how memory laid out when a program is executed. Keep reading.

## Assembly

Assembly language is a low-level programming language for a computer.

We will use 32-bit Intel processor.

The x86 processor ( 32-bit processor ) has several registers, which are like internal variables for the processor.

*Registers* are responsible for manipulations that allow modern computers to function, and can be manipulated with assembly instructions.

*Registers* can be grouped into four categories.

1. General purpose
2. Segment
3. Control

The most critical registers are the General registers.In these, there are further subdivisions into Data registers, Pointer registers, and Index registers.

### General Purpose Registers

- Data registers
    
    EAX ( Accumulator )
    
    EBX ( Base )
    
    ECX ( Counter )
    
    EDX ( Data )
    
- Pointer registers
    
    ESP ( Extended Stack Pointer )
    
    EBP ( Extended Base Pointer )
    
    EIP  ( Extended Instruction Pointer )
    

- Index registers
    
    ESI **( Extended Source Index )
    
    EDI **( Extended Destination Index )
    

If you are familiar with General Purpose Registers, you can examine memory addresses.

So,let's skip some parts of assembly.

The assembly instructions in Intel syntax generally follow this style:

operation <destination> , <source>

The destination and source values will either be a register, a memory address, or a value.

operations :

**mov** operation will **move** a value from source to destination.

**sub** operation will **subtract.**

**inc** operation will **increase.**

**ret** operation will **return** address**.**

**cmp** operation will **compare** values.

**jle** operation, *jump-if-less-than-or-equal*, referring to the result of the previous comparison.

**jmp** operation will **jump** to another value.

For example :

```bash
804838b:    83 7d fc 09     cmp      DWORD PTR [ebp-4],0x9
804838f:    7e 02           jle      8048393 <main+0x1f>
8048391:    eb 13           jmp      80483a6 <main+0x32>
```

This assembly instruction will compare 4-byte value located at the address ebp-4 with number 9.

if that value is less than or equal to  ( distinction ),it will jump to address 8048393,otherwise,jump to 80483a6.

P.S There left many operations.You should read yourself.

## Debugger

It's better to see for yourself.Let's use a debugger.

Debuggers are used by programmers to step through compiled programs, examine program memory, and view processor registers.

Debuggers can view the execution from all angles, pause it, and change anything along the way.

We will use *GNU debugger,GDB*.

We would like to use intel syntax.

```bash
root@zus:Desktop/memory_layout # echo "set disassembly-flavor intel" > ~/.gdbinit
root@zus:Desktop/memory_layout # cat ~/.gdbinit
set disassembly-flavor intel
root@zus:Desktop/memory_layout #
```

Now,GDB is configured to use Intel syntax.

```c
void stack_function(int a, int b, int c, int d)
{

}

int main() 
{
        stack_function(1, 2, 3, 4);
}
```

This simple program first declares a function named *stack_function* which expects 4 integer variables as arguments,then *main* function called that function.

Remember .*stack* segment stores function arguments.

Now, compile this program with GCC compiler.

The `-g` flag can be used by the GCC compiler to include extra debugging information ,which will give GDB access to the source code.

To see the source code on GDB,use `list` cmd.

```bash
root@zus:Desktop/memory_layout # gdb -q stack                
Reading symbols from stack...
(gdb) list
1       void stack_function(int a, int b, int c, int d)
2       {
3
4       }
5
6       int main() 
7       {
8               stack_function(1, 2, 3,4);
9       }
(gdb)
```

To disassemble in GDB, use `disass` or `disassemble` command.

Let's disassemble the *main* function. 

```bash
(gdb) disass main
Dump of assembler code for function main:
   0x00001199 <+0>:     push   ebp
   0x0000119a <+1>:     mov    ebp,esp
   0x0000119c <+3>:     call   0x11bd <__x86.get_pc_thunk.ax>
   0x000011a1 <+8>:     add    eax,0x2e5f
   0x000011a6 <+13>:    push   0x4
   0x000011a8 <+15>:    push   0x3
   0x000011aa <+17>:    push   0x2
   0x000011ac <+19>:    push   0x1
   0x000011ae <+21>:    call   0x1189 <stack_function>
   0x000011b3 <+26>:    add    esp,0x10
   0x000011b6 <+29>:    mov    eax,0x0
   0x000011bb <+34>:    leave  
   0x000011bc <+35>:    ret    
End of assembler dump.
(gdb)
```

This is the dump of assembler code for *main* function. 

We are not going to explain how to use debugger in depth.

For more information, read the official documentation.

## Stack

Stack is *Last-In-First-Out* (LIFO) data structure.

The stack starts with a high address and grows towards lower memory addresses as values are added.

The Base Pointer points to the beginning (base) of the stack.

The Stack Pointer points to the top of the stack.

Data is written to the stack segment by using `PUSH` instruction.

Data is removed from the stack segment by using `POP` instruction.

Let's see it.

Run the following program in GDB and learn how they really work.

```c
//stack.c

void stack_function(int a, int b, int c, int d)
{

}

int main() 
{
        stack_function(1, 2, 3, 4);
}
```

```bash
root@zus:Desktop/memory_layout # gdb -q ./stack
Reading symbols from ./stack...
(gdb) disass main
Dump of assembler code for function main:
   0x00001199 <+0>:     push   ebp
   0x0000119a <+1>:     mov    ebp,esp
   0x0000119c <+3>:     call   0x11bd <__x86.get_pc_thunk.ax>
   0x000011a1 <+8>:     add    eax,0x2e5f
   0x000011a6 <+13>:    push   0x4
   0x000011a8 <+15>:    push   0x3
   0x000011aa <+17>:    push   0x2
   0x000011ac <+19>:    push   0x1
   0x000011ae <+21>:    call   0x1189 <stack_function>
   0x000011b3 <+26>:    add    esp,0x10
   0x000011b6 <+29>:    mov    eax,0x0
   0x000011bb <+34>:    leave  
   0x000011bc <+35>:    ret    
End of assembler dump.
(gdb)
```

Since we call argument 1,2,3,4 in sequence, integer 1 (0x1) is pushed first, becomes the last item and the stack grows toward lower memory addresses.

Remember .*stack* segment is  *Last-In-First-Out* (LIFO) and is used to store local variables,function arguments.

## Heap

The heap is *First-In-First-Out* (FIFO) data structure.

 The heap starts with a low address and grows downward to high memory addresses as values are added.

Blocks of memory in .*heap* segment can be allocated and used for whatever we need.

Allocating memory on the heap is done using the `malloc()` function.

This function accepts size as its only argument and reserves that much space in .*heap* segment, return the address to the start of this memory as void pointer.

malloc function returns void pointer which points to newly created memory address ,so we need to typecast to appropriate data type.

The corresponding deallocation function is `free()` function.

This function accepts a pointer as its only argument and free that memory space on .*heap* segment.

These functions are demonstrated below.

```c
//heap.c

#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int main()
{

	int mem_size = 20;

	char *char_ptr;

	char_ptr = (char *)malloc(mem_size); // typecast malloc's return address
	printf("\n[+] Allocating %d bytes on heap.\n", mem_size);

	strcpy(char_ptr,"Hello World!");
	printf("    char_ptr points to ( %p ) ~> %s\n",char_ptr,char_ptr);

	printf("\n[-] Freeing char_ptr's memory\n");
	free(char_ptr);
	printf("    char_ptr points to ( %p ) ~> %s\n\n",char_ptr,char_ptr);

	return 0;
}
```

```bash
root@zus:Desktop/memory_layout # ./heap      

[+] Allocating 20 bytes on heap.
    char_ptr points to ( 0x5686c1a0 ) ~> Hello World!

[-] Freeing char_ptr's memory
    char_ptr points to ( 0x5686c1a0 ) ~> 

root@zus:Desktop/memory_layout #
```

Here we add some data to the heap segment using `malloc()` function and then remove using `free()` function.

The following part will study how the heap segment store data in memory.

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int main()
{
	printf("\nHeap is FIFO [ First-In-First-Out ] data structure.\nHeap grows downward (towards higher addresses.)\n\nLow memory address\n\n");

	int *int_ptr,*int_ptr1,*int_ptr2;

	int memory_size;

	memory_size = 10;

	int_ptr = (int *)malloc(memory_size);
	*int_ptr = 127;

	printf("[ Allocated %d bytes of memory on heap ] : int_ptr    (0x%x) ~> %d\n",memory_size,int_ptr,*int_ptr );

	int_ptr1 = (int *)malloc(memory_size);
	*int_ptr1 = 10;

	printf("[ Allocated %d bytes of memory on heap ] : int_ptr1   (0x%x) ~> %d\n",memory_size,int_ptr1,*int_ptr1 );

	int_ptr2 = (int *)malloc(memory_size);
	*int_ptr2 = 1337;

	printf("[ Allocated %d bytes of memory on heap ] : int_ptr2   (0x%x) ~> %d\n",memory_size,int_ptr2,*int_ptr2 );

	printf("\nHigh memory address\n\n");

}
```

```bash
root@zus:Desktop/memory_layout # ./heap_FIFO 

Heap is FIFO [ First-In-First-Out ] data structure.
Heap grows downward (towards higher addresses.)

Low memory address

[ Allocated 10 bytes of memory on heap ] : int_ptr    (0x568055b0) ~> 127
[ Allocated 10 bytes of memory on heap ] : int_ptr1   (0x568055c0) ~> 10
[ Allocated 10 bytes of memory on heap ] : int_ptr2   (0x568055d0) ~> 1337

High memory address

root@zus:Desktop/memory_layout #
```

Take a look at those addresses (Highlight in red) on this output.

Every time we call `malloc()` function, heap grows toward higher addresses.

These are all I understand.

Try Harder.
