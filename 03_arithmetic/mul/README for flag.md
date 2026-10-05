# Multiplication

The three programs that used the mul function

01_mul_8bit: multiplies 25 by 10

02_mul_16bit: multiplies 3000 by 200

03_mul_32bit: multiplies 100000 by 300000

# Flags set

- mul1: none
- mul2: Carry Flag  and Overflow Flag
- mul3: Carry Flag and Overflow Flag 

## Reason for this

mul gives a result twice as wide as its inputs. Thus the Carry Flag and Overflow Flag are set when the upper half of that result is not zero, which means the answer does not fit in the lower half. Otherwise the flags are cleared..

mul1: the answer, 250, fits in the lower half, so nothing is set.
mul2: the answer, 600000, needs the upper half (DX), so Carry Flag and Overflow Flag are set.
mul3: the answer, 30000000000, needs the upper half (EDX), so Carry Flag and Overflow Flag are set.

The other flags are undefined after mul.

At the end of each program, xor ebx, ebx sets Zero Flag. It sets ebx to 0, which is the exit code.