# ISCP

## A nice stable alt to interrupt-based syscalls.

### How does it work?
Inside the process's control block, there will
be a stack or array which we will refer to as the buffer.

The buffer can hold up to n arguments (n can be any amount defined by the operating system).


The ISCP endpoint/port/buffer has one reserved field which is the magic number.
This magic number is usually 0x15C9 and should only be placed on the top of
the buffer if the process finished it's request and wants it handled.

#### Stack layout:

- 0x15CP     (top: magic)
- SyscallID
- Args
- ..

If a request is already pending (i.e the top of the buffer is not clear) then you should poll
the value at the top of the buffer until it is zero, if you have the timer (example: PIT) frequency at 100 then 
the lock will only be about ~10ms.

During the context switch is when the ISCP packet should be handled (an ISCP packet is just the arguments you pass in the buffer together).

IMPORTANT: Before returning to userland, clear the buffer.
