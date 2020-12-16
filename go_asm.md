# Go Assmably

## 特殊标识符

- SP：Stack Pointer -> refer to **frame-local variables** and the **arguments** being prepared **for function calls**
  - 
- SB：Static Base    ->   **origin** of the memory
  - foo(SB): represents address in memory of  the name foo
  - foo+4(SB): represents the address that four bytes past the start of the foo.
- FP：Frame Pointer    -> refer to the **arguments on the stack** as offsets from the pseudo-register.
  - 
- PC: Program counter

