# lab_architecture_archlab
4  Part A — Week 1
You will be working in directory sim/misc in this part.

Your task is to write and simulate the following three Y86-64 programs. The required behavior of these programs is defined by the example C functions in examples.c. Be sure to put your name and ID in a comment at the beginning of each program. You can test your programs by first assemblying them with the program yas and then running them with the instruction set simulator yis.

expr.ys: Warming up with arithmetic expressions and conditionals
Write a program (expr.ys) that computes the C expression:

   y = x > 5 ? x+1 : x;
Test you program with the two test cases below:

Test 1 (should terminate with y == 1):
.pos 100
x: .quad 0x01
y: .quad 0x00
Test 2 (should terminate with y == 13):
.pos 100
x: .quad 0x0c
y: .quad 0x00
Assembly it by issuing (inside the directory misc):

  $ ./yas expr.ys
Ther generated object file expr.yo, will be a memory dump where the instructions have been encoded starting from memory location 0, and 16 bytes have been reserved for the variables x and y starting at location 0x64 (= 100 in hex) and initialised with c and 0. You can then run the instruction simulator with:

  $ ./yis expr.yo
This will print the final state reached by the program (you cannot do step-by-step with the instruction simulator). Alternatively, you can run the simulator of the SEQ architecture with:

  $ (cd ../seq; ./ssim -g ../misc/expr.yo)
Observe that both the instruction simulator and the SEQ simulator require an object file.

copy.ys: Copy a source block to a destination block
Write a program (copy.ys) that copies a block of words from one part of memory to another (non-overlapping) area of memory, computing the checksum (Xor) of all the words copied.

Your program should consist of code that sets up a stack frame, invokes a function copy_block, and then halts. The function should be functionally equivalent to the C code shown below, where len, src, dest, and result are global variables:

  00   /* copy_block - Copy src to dest and return xor checksum of src */
  03   result = 0;
  04   while (len > 0) {
  05       int val = *src++;
  06       *dest++ = val;
  07       result ^= val;
  08       len--;
  09   }
Test your program using the following three-element source and destination blocks:


.align 8
# Source block
src:
        .quad 0x00a
        .quad 0x0b0
        .quad 0xc00

# Destination block
dest:
        .quad 0x111
        .quad 0x222
        .quad 0x333
To understand the memory layout of the example, remember that an array is stored in memory by placing contiguosly (at increasing addresses) its content: the example corresponds to the familiar C declaration:

int src[] = {0x00a, 0x0b0, 0xc00};
int dst[] = {0x111, 0x222, 0x333};
int len = 3;
The address of the array elements can be computed by adding on offset to the base address of the array. In the example, as src is stored from location 100, we get that src[0] is at address 100, src[1] at 108 (that is 100 plus the with of one quad int which is 8), src[2] at 110. The addressing mode D(rA) enables an easy computation of these addresses: the constant D will be the address of src (statically computed by the assembler pass), and the offset can be kept in the register rA.

5  Part B
You will be working in directory sim/seq in this part.

Your task in Part B is to extend the SEQ processor to support the iaddq, with the following format:



This instruction adds the constant value V to register rB.

To add this instructions, you will modify the file seq-full.hcl, which implements the version of SEQ described in the CS:APP3e textbook for the Y86-64 architecture. In addition, it contains declarations of some constants that you will need for your solution.

Your HCL file must begin with a header comment containing the following information:

Your name and ID.
A description of the computations required for the iaddq instruction. You can use the descriptions of irmovq and OPq in Figure 4.18 in the CS:APP3e text as a guide.
