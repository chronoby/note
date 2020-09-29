# Process

### Process 
    a program in execution; process execution must progress in sequential fashion
### A process includes:
- text section (code)
- program counter 
- stack (function parameters, local vars, return addresses)
- data section (global vars)
- heap (dynamically allocated memory)

### As a process executes, it changes state
- new:  The process is being created
- running:  Instructions are being executed
- waiting:  The process is waiting/blocked for some event to occur
- ready:  The process is waiting to be assigned to a processor
- terminated:  The process has finished execution
