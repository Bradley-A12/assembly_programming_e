# 02 Observation and analysis
---
This program shows how the stack is used for a function call and a local stack frame. `_start` calls `work`, which saves the old base pointer. It also creates 16 bytes of local space, stores 100 and 200 in that space, loads them into rax and rbx, then tears the frame down and returns. Unlike the previous program, the values 100 and 200 are never pushed: they are written into space reserved by `sub rsp, 16`.

---
__*Initial state (_start):*__ rsp = 0x7fffffffdd20. The stack is not empty: it already holds argc, argv and the environment placed there by the kernel. Everything below pushes to addresses under this one.

---
__*call work:*__ The return address 0x401005, it is the address that `mov rax, 60` is pushed. rsp = 0x7fffffffdd18 and the top of the stack is the return address.

---
__*push rbp:*__ The caller's rbp is saved. rsp = 0x7fffffffdd10 and the top is the old rbp value.

---
__*mov rbp, rsp:*__ Does not change the stack. rbp now equals rsp = 0x7fffffffdd10 and marks the base of the frame.

---
__*sub rsp, 16:*__ Reserves 16 bytes of local space by moving rsp down without writing anything. rsp = 0x7fffffffdd00. The space at [rbp-8] and [rbp-16] now belongs to the frame.

---
__*mov qword [rbp - 8], 100:*__ Writes 100 to 0x7fffffffdd08. rsp does not change, because this is a write at an offset from rbp, not a push.

---
__*mov qword [rbp - 16], 200:*__ Writes 200 to 0x7fffffffdd00. rsp doesn't change.

---
__*mov rax, [rbp - 8] / mov rbx, [rbp - 16]:*__ Reads the values back into registers (rax = 100, rbx = 200). The stack and rsp are unchanged. Here, only the memory is read

---
__*mov rsp, rbp:*__ Discards the local space by moving rsp back up. rsp = 0x7fffffffdd10. The 100 and 200 are no longer part of the stack but remain in memory.

---
__*pop rbp:*__ The saved rbp is copied back into rbp and rsp moves up by 8. rsp = 0x7fffffffdd18 and the top is the return address.

---
__*ret:*__ The return address is popped into rip and rsp moves up by 8. rsp = 0x7fffffffdd20, the starting value, and execution continues at `mov rax, 60`.

---


## Purpose of the stack in this program
The stack is used for holding the return address so `ret` knows where to go back to after `work`. But it also holds the local variables of `work` in a frame between rbp and rsp. 

## RSP's change
RSP changes the way it does because the stack grows toward lower addresses. `call` and `push` perform rsp -= 8 and then write, while `pop` and `ret` read and then perform rsp += 8. `sub rsp, 16` and `mov rsp, rbp` move rsp directly by an arbitrary amount instead of 8. 


## Showing that the values are not deleted
The following commands were used to show the values after the operations:
```
(gdb) x/2gd $rsp      #after the nop at the end of the frame
0x7fffffffdd00: 200  100
(gdb) x/2gd $rsp-16     # after ret
0x7fffffffdd08: 100  0
```
