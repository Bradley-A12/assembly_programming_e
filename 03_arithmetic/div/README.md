# Division

The two programs that use the div instruction.

01_div_8bit: divides 100 by 7

02_div_16bit: divides 50000 by 300

03_div_32bit: divides 300000000 by 1000

## Flag set

-  The Zero Flag is set.

## Reason for this

"div" does not set any flags you can rely on. The flag comes from xor ebx, ebx at the end of each program. This sets ebx to 0, which is the exit code. Since the result is 0, Zero Flag becomes 1.


