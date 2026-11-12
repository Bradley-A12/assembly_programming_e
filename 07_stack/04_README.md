# 04 Observation and analysis
---
A 32-bit version of the stack-frame program in 02. The difference is that this is 32-bit code, so the stack uses ESP and EBP, and every stack entry is 4 bytes instead of 8.

---
__*_start:*__ esp = 0xffffce60 . The stack is not empty.

---
__*call work:*__ The return address, 0x8049005 is pushed. esp = 0xffffce5c and the top of the stack is the return address.

---
__*push ebp:*__ The caller's ebp is saved. esp = 0xffffce58 and the top is the old ebp value.

---
__*mov ebp, esp:*__ Does not change the stack. ebp = esp = 0xffffce58 and marks the base of the frame.

---
__*sub esp, 8:*__ Reserves 8 bytes of local space by moving esp down without writing anything. esp = 0xffffce50. The space at [ebp-4] and [ebp-8] now belongs to the frame.

---
__*mov dword [ebp - 4], 100:*__ Writes 100 to 0xffffce54. esp does not change, because this is a write at an offset from ebp, not a push.

---
__*mov dword [ebp - 8], 200:*__ Writes 200 to 0xffffce50. esp does not change.

---
__*mov eax, [ebp - 4] / mov ebx, [ebp - 8]:*__ Reads the values back into registers i.e. eax = 100, ebx = 200. The stack and esp are unchanged. The memory is only read.

---
__*mov esp, ebp:*__ Discards the local space by moving esp back up. esp = 0xffffce58. The 100 and 200 are no longer part of the stack but remain in memory.

---
__*pop ebp:*__ The saved ebp is copied back into ebp and esp moves up by 4. esp = 0xffffce5c and the top is the return address.

---
__*ret:*__ The return address is popped into eip and esp moves up by 4. esp = 0xffffce60, the starting value, and execution continues at `mov eax, 1`.

---

## Purpose of the stack in this program
The stack is used for two things: holding the return address so `ret` knows where to go back to after `work`, and holding the local variables of `work` in a frame between ebp and esp.

## ESP's change
The stack grows toward lower addresses. `call` and `push` perform esp -= 4 and then write, while `pop` and `ret` read and then perform esp += 4. `sub esp, 8` and `mov esp, ebp` move esp directly by an arbitrary amount instead of 4.

## Showing that the values are not deleted
Using the following commands:
```
(gdb) x/2wd $esp        # at the nop
0xffffce50: 200  100
(gdb) x/2wd $esp-16     # after ret
0xffffce50: 200  100
```