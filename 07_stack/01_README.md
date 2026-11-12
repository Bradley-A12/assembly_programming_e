# 01 Observation and analysis
---
This  program is used to showcase the working of the stack, by pushing two values, 100 and 200, from rax and rbx respectively to the stack. Then, the values are popped from the stack into rcx and rdx respectively. We notice that the values come out reversed due to the last-in-first-out structure of the stack.

---
__*mov rax/rbx:*__ Does not change the stack, rsp = 0x7fffffffdd20

---
__*push rax:*__ the stack is changed, the top becomes 100 and rsp = 0x7fffffffdd18

---
__*push rbx:*__ Stack changes again, top becomes 200 and rsp = 0x7fffffffdd10

---
__*pop rcx:*__ Stack changes as the top value, 200 is removed and the new top becomes 100. The rsp = 0x7fffffffdd18

---
__*pop rdx:*__ Stack changes, the top value 100 is removed and the stack is back at its starting value. The rsp = 0x7fffffffdd20

---

## RSP's change
RSP changes the way it does due to  the fact that the stack grows toward lower addresses, and pushing basically performs a rsp -=8 operation while popping does a rsp += 8 as shows by how the rsp moves 8 bytes from each entry i.e. dd18 → dd10

## Showing that the values are not deleted

Using the following commands:
```
(gdb) x/2gd $rsp        
0x7fffffffdd10: 200     100
(gdb) x/2gd $rsp-16
0x7fffffffdd10: 200     100
```

The first, after the first set of pushes and the second after the last set of pops, it is observed that the values are still kept in a memory location below rsp