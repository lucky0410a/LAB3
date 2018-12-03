# ARM calling convention
### • Register usage:
![](https://i.imgur.com/iuXhZ4J.png)
#### • If a routine has more than 4 arguments R0-R3 are used for the first 4 arguments and the rest are placed on the stack before the call
#### • The stack must be of the Full-Descending type
  A **Descending** stack grows downwards. It starts from a high memory address, and as items are pushed onto it, progresses to lower memory addresses. The previous examples have been of a Descending stack.
  In a **Full** stack, the stack pointer points to the topmost item in the stack, i.e. the location of the last item to be pushed onto the stack.
#### • Local variables can also be stored in R0-R3, R12, and even LR, specially in “leaf” subroutines (no other subroutine call)

### 4.3 Calling assembly functions from C and C++
#### Often, all the code for a single application is written in the same source language. This is usually a high-level language such as C or C++ which is then compiled to ARM assembly code.

#### However, in some situations you might want to make function calls from C/C++ code to assembly code. For example:
#### • If you want to make use of existing assembly code, but the rest of your project is in C or C++.
#### • If you want to manually write critical functions directly in assembly code that can produce better optimized code than compiling C or C++ code.
#### • If you want to interface directly with device hardware and if this is easier in low-level assembly code than high-level C or C++.
#### For code portability, it is better to use intrinsics or inline assembly rather than writing and calling assembly functions.
#### To call an assembly function from C or C++:
#### 1. In the assembly source, declare the code as a global function using .globl and .type:
```
	.globl   myadd
     .p2align 2
	.type    myadd,%function
			
myadd:                     // Function "myadd" entry point.
	.fnstart
	add      r0, r0, r1   // Function arguments are in R0 and R1. Add together and put the result in R0.
	bx       lr           // Return by branching to the address in the link register.
	.fnend
```
#### Note:
#### armclang requires that you explicitly specify the types of exported symbols using the .type directive. If the .type directive is not specified in the above example, the linker outputs warnings of the form:
#### Warning: L6437W: Relocation #RELA:1 in test.o(.text) with respect to myadd...
#### Warning: L6318W: test.o(.text) contains branch to a non-code symbol myadd.
#### 2. In C code, declare the external function using extern:
```
#include <stdio.h>
					
extern int myadd(int a, int b);
					
int main()
{
	int a = 4;
	int b = 5;
	printf("Adding %d and %d results in %d\n", a, b, myadd(a, b));
	return (0);
}
```
#### In C++ code, use extern "C":
```
extern "C" int myadd(int a, int b);
```
#### 3. Ensure that your assembly code complies with the Procedure Call Standard for the ARM Architecture (AAPCS).
#### The AAPCS describes a contract between caller functions and callee functions. For example, for integer or pointer types, it specifies that:
#### • Registers R0-R3 pass argument values to the callee function, with subsequent arguments passed on the stack.
#### • Register R0 passes the result value back to the caller function.
#### • Caller functions must preserve R0-R3 and R12, because these registers are allowed to be corrupted by the callee function.
#### • Callee functions must preserve R4-R11 and LR, because these registers are not allowed to be corrupted by the callee function.
#### For more information, see the [Procedure Call Standard for the ARM Architecture (AAPCS)](http://infocenter.arm.com/help/index.jsp?topic=/com.arm.doc.subset.swdev.abi/index.html).
#### 4. Compile both source files:
```
armclang --target=arm-arm-none-eabi -march=armv8-a main.c myadd.s
```

