# 06 Observation and analysis
---
This program is the 32-bit version of the previous push/pop program. The difference from the 64-bit version is that this is 32-bit code, so the stack uses ESP and every entry is 4 bytes instead of 8.

---
__*Initial state (_start):*__ esp = S = [PASTE: p/x $esp]. The stack is not empty: it already holds argc, argv and the environment placed there by the kernel. Everything pushed goes to addresses below this one.

---
__*mov eax/ebx:*__ Does not change the stack, esp = S. eax = 0x11223344 and ebx = 0x1234.

---
__*push eax:*__ esp = S-4 and the top of the stack is 0x11223344.

---
__*push ebx:*__ esp = S-8 and the top is 0x1234, with eax's value below it. ebx is stored as a full 4 bytes (0x00001234) even though the value is small, because every 32-bit push is 4 bytes.

---
__*nop:*__ Marker for inspecting the stack: x/2wx $esp shows 0x00001234 then 0x11223344.

---
__*pop ebx:*__ 0x1234 is copied from the top of the stack into ebx and esp moves up by 4. esp = S-4 and the top is 0x11223344.

---
__*pop eax:*__ 0x11223344 is copied into eax and esp moves up by 4. esp = S, the starting value. Both registers hold their original values again.

---
__*End of program:*__ `mov eax, 1`, `xor ebx, ebx` and `int 0x80` do not touch the stack. They overwrite eax and ebx (so the restored values are lost) and the program calls exit(0) with esp balanced at its starting value. Syscall number 1 is exit on 32-bit Linux.

---

## Purpose of the stack in this program
The stack is used as temporary storage for two register values. Because the pops are in the reverse order of the pushes, each value goes back into the register it came from. Compare with the first program, where popping into `rcx` then `rdx` swapped the order.

## ESP's change
The stack grows toward lower addresses. `push` performs esp -= 4 and then writes, while `pop` reads and then performs esp += 4.



## Byte order on the stack
x86 is little-endian, so the least significant byte is stored at the lowest address. Using:
```
(gdb) x/8xb $esp       # at the nop, after both pushes
 34 12 00 00 | 44 33 22 11
```