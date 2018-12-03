# ARM calling convention
### • Register usage:
![](https://i.imgur.com/iuXhZ4J.png)
#### • If a routine has more than 4 arguments R0-R3 are used for the first 4 arguments and the rest are placed on the stack before the call
#### • The stack must be of the Full-Descending type
#### • Local variables can also be stored in R0-R3, R12, and even LR, specially in “leaf” subroutines (no other subroutine call)

### 4.3 Calling assembly functions from C and C++
#### Often, all the code for a single application is written in the same source language. This is usually a high-level language such as C or C++ which is then compiled to ARM assembly code.

#### However, in some situations you might want to make function calls from C/C++ code to assembly code. For example:
####     • If you want to make use of existing assembly code, but the rest of your project is in C or C++.
