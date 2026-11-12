# 03 Observation and analysis
---
This program shows the stack being used purely for a function call and return. `_start` loads 10 into rax and calls `calculate`, which adds 5 and returns. The result,rax = 15 is checked at `after_call`, then the program exits. The only stack activity is the return address that `call` saves and `ret` restores.

---
__*Initial state (_start):*__ rsp = 0x7fffffffdd20. The stack is not empty.

---
__*mov rax, 10:*__ Does not change the stack, rsp = 0x7fffffffdd20.

---
__*call calculate:*__ The address of the next instruction, `after_call` → 0x40100a, is pushed as the return address. The rsp = 0x7fffffffdd18 and the top of the stack is the return address.

---
__*add rax, 5:*__ Does not change the stack, rsp = 0x7fffffffdd18. rax becomes 15. The return address is still the top.

---
__*ret:*__ The return address is copied from the top of the stack into rip and rsp moves up by 8. rsp = 0x7fffffffdd20.

---
__*after_call (nop):*__ rax = 15 and rsp = 0x7fffffffdd20. The stack is balanced.

---
__*End of program:*__  The program calls exit(0) with rsp balanced at its starting value.

---

## Purpose of the stack in this program
The stack is used for one thing: remembering where to resume. `calculate` has no idea who called it, so `call` stores the return address on the stack and `ret` reads it back. The result is passed through `rax`, not the stack.

## RSP's change
The stack grows toward lower addresses. `call` performs rsp -= 8 and then writes the return address, while `ret` reads it into rip and then performs rsp += 8.


## Showing that the value is not deleted
Using the following commands:
```
(gdb) x/gx $rsp         # right after call
 0x7fffffffdd18: 0x000000000040100a
(gdb) x/gx $rsp-8       # after ret, at the after_call
 0x7fffffffdd18: 0x000000000040100a
```

