# Subtraction

The three programs that used sub and sbb.

 01_sub_8bit: subtracts 80 from 50

 02_sub_16bit: subtracts 2000 from 1000
 
 03_sub_borrow: subtracts 1 from 0, then uses sbb

## Flags set

In all three programs, the Carry Flag and Sign Flag are set by the subtraction.

## Reason

In each program the first number is smaller than the second, therefore, the result is less than zero.

- Carry Flag is set when the first number is smaller than the second (an unsigned borrow).
- Sign Flag is set when the top bit of the result is 1, which is what a negative result looks like.

OF is not set, because each result still fits as a signed number.

At the end of each program, xor ebx, ebx sets Zero Flag. It sets ebx to 0, which is the exit code.