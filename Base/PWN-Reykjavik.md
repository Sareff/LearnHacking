202102181236
Tags: #ctf #pwn #easy

Challenge Link: https://ctflearn.com/challenge/990

---
# Reykjavik

The task is based on pwn ELF program called "Reykjavik". We need to find flag inside the program using some kind of debugger. I used GDB for the sake of learning and lack of other more complex tools and skills in them.

First of all let's launch GDB:
```bash
gdb Reykjavik
```

Before we start any kind of reverse engineering I suggest to change syntax of assembler. GDB usually use AT&T syntax which I found a little odd and ugly, so I change it to Intel syntax by this command:
```gdb
set disassembly-flavor intel
```

Now we need to disassemble the main function of the program:
```gdb
disassemble main
```

We will see something like this:

![[Disasm Main.png]]

Next we need to set a breakpoint on the start of main function:
```gdb
break main
```

So, simply put, now we need to run a program with input:
```gdb
run CTFlearn{test}
```

You see, this program just checking the flag what we've provided and compare it to a flag we need to steal. It firstly checks if you input any flag and then compare it. All of this we can understand just from reading disassembled program. The lines with  strings like ```<__printf_chk@plt>```, ```<puts@plt>``` signalize us that after this lines executed we'll get some text message in terminal. Try set breaks on this lines to see what he actual text we will get from each of it if you wish. This can be achieved by commands:
```gdb
break *(the memmory number you wish to set a breakpoint on)
```

To go trough the program cycle we can use ```si``` and ```ni```. 
But now I want to avoid to identify every string and which line of code it prints and come to a main part. You see, when we compare strings, it usually done with help of function called ```<strcmp@plt>```.  This function return two numbers - 0 and 1. 0 means that the strings are the same, and 1 means they are not. This number after function executes saved in register (on screenshot this register is ```r12d```). After that, we can see that the register that contains the number are "tested" with assembler operator ```test```. If it's 0 we will get our sacred flag, if 1 than we will kicked out (```jne``` operator signalize it). All we have to do on this step is just set a breakpoint before test (on line with ```test``` operator) and change the value of register to ```0x0```. It can be achieved with this commands:
```gdb
break *0x555555555170
run CTFlearn{test}
continue
set $eax = 0
continue
```

---
### Links
-