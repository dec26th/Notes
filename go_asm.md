# Go Assmably

## Command to gen Go Asm Code

 `go tool compile -S xxx.go`   show the assmably of xxx.go in terminal



```shell
go tool compile -N -l once.go    // generate the assmably file
go tool objdump once.o           // show the assmably file
go tool objdump -S Do once.o     // show the function Do in the assmably file
```



## Symbols

- SP：Stack Pointer -> refer to **frame-local variables** and the **arguments** being prepared **for function calls**
  - 
- SB：Static Base    ->   **origin** of the memory
  - foo(SB): represents address in memory of  the name foo
  - foo+4(SB): represents the address that four bytes past the start of the foo.
- FP：Frame Pointer    -> refer to the **arguments on the stack** as offsets from the pseudo-register.
  - 
- PC: Program counter

