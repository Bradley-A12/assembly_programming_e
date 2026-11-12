# 05 Observation and analysis
---
This program shows that matched pushes and pops restore the original values when the pop order is the reverse of the push order. It loads a 64-bit constant (0x1122334455667788) into `rax` and 0x1234 into `rbx`, pushes both, then pops them back into `rbx` and `rax`. Unlike the first program, nothing is swapped: `rbx` was pushed last and is popped first, into `rbx`, so each value returns to the register it came from. This is the standard pattern for saving and restoring registers.

---
__*(_start):*__ rsp = 0x7fffffffdd20. The stack is not empty: it already holds argc, argv and the environment placed there by the kernel. Everything pushed goes to addresses below this one.

---
__*mov rax/rbx:*__ Does not change the stack, rsp = 0x7fffffffdd20. rax = 0x1122334455667788 and rbx = 0x1234.

---
__*push rax:*__ rsp = 0x7fffffffdd18 and the top of the stack is 0x1122334455667788.

---
__*push rbx:*__ rsp = 0x7fffffffdd10 and the top is 0x1234, with rax's value below it. rbx is stored as a full 8 bytes (0x0000000000001234) even though the value is small, because every 64-bit push is 8 bytes.

---
__*nop:*__ Marker for inspecting the stack: x/2gx $rsp shows 0x1234 then 0x1122334455667788.

---
__*pop rbx:*__ 0x1234 is copied from the top of the stack into rbx and rsp moves up by 8. rsp = 0x7fffffffdd18 and the top is 0x1122334455667788.

---
__*pop rax:*__ 0x1122334455667788 is copied into rax and rsp moves up by 8. rsp = 0x7fffffffdd20, the starting value. Both registers hold their original values again.

---
__*End of program:*__ `mov rax, 60`, `xor rdi, rdi` and `syscall` do not touch the stack. They overwrite rax, the restored value is therefore lost. And the program calls exit(0) with rsp balanced at its starting value.

---

## Purpose of the stack in this program
The stack is used as temporary storage for two register values. Because the pops are in the reverse order of the pushes, each value goes back into the register it came from. Compare with the first program, where popping into `rcx` then `rdx` swapped the order.

## RSP's change
The stack grows toward lower addresses. `push` performs rsp -= 8 and then writes, while `pop` reads and then performs rsp += 8.


## Byte order on the stack
x86 is little-endian, so the least significant byte is stored at the lowest address. Using:
```
(gdb) x/16xb $rsp       # at the nop, after both pushes
 34 12 00 00 00 00 00 00 | 88 77 66 55 44 33 22 11
```


