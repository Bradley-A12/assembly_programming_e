# Addition

The three programs that used add and adc.

01_add_8bit: adds 120 and 10

02_add_16bit: adds 32000 and 500

03_add_carry: adds 65535 and 1, then uses adc


## Flags set

add1: Overflow Flag  and Sign Flag 
add2: none from the addition
add3: Carry Flag and Zero Flag 


add sets flags based on its result.
- Overflow Flag is set when a signed result does not fit.
- Sign Flag is set when the top bit of the result is 1.
- Carry Flag is set when an unsigned result does not fit.
- Zero Flag is set when the result is 0.

At the end of each program, xor ebx, ebx also sets Zero Flag. It sets ebx to 0.