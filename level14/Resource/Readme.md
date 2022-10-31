# level14
We are greeted with... nothing.  
No file in the home, nothing belonging to user `level14` or `flag14`.  
As we consider giving up with our hope fading away, the thought that something ridiculous still hasn't already been attempted comes to mind.
Let's hack `getflag`.

Now if there's something we know about `getflag`, it's that it prints a different flag for each user.  
So, maybe getting the correct flag is just as simple as altering the output of the call to `getuid`?

The `LD_PRELOAD` hack doesn't work here since there are counter measures in the binary.  
So we will have to dig in.


# Resolution
Let's fire up `gdb`. 

`gdb getflag`  

What we want to do first is stop on main, see what comes up, relying heavily on the different libc function calls to get an understanding of the flow and of what to alter.
```gdb
(gdb) break main
Breakpoint 1 at 0x804894a
(gdb) run
Starting program: /bin/getflag 

Breakpoint 1, 0x0804894a in main ()
(gdb) disas
Dump of assembler code for function main:
   0x08048946 <+0>:		push   %ebp
   0x08048947 <+1>:		mov    %esp,%ebp
   0x08048949 <+3>:		push   %ebx
=> 0x0804894a <+4>:		and    $0xfffffff0,%esp
   0x0804894d <+7>:		sub    $0x120,%esp
   0x08048953 <+13>:	mov    %gs:0x14,%eax
   0x08048959 <+19>:	mov    %eax,0x11c(%esp)
   0x08048960 <+26>:	xor    %eax,%eax
   0x08048962 <+28>:	movl   $0x0,0x10(%esp)
   0x0804896a <+36>:	movl   $0x0,0xc(%esp)
   0x08048972 <+44>:	movl   $0x1,0x8(%esp)
   0x0804897a <+52>:	movl   $0x0,0x4(%esp)
   0x08048982 <+60>:	movl   $0x0,(%esp)
   0x08048989 <+67>:	call   0x8048540 <ptrace@plt>
   0x0804898e <+72>:	test   %eax,%eax
   0x08048990 <+74>:	jns    0x80489a8 <main+98>
```
There's a call to `ptrace` coming up!  
Since it's unlikely `getflag` intends to trace a child process, it probably is a countermeasure for detecting the use of a debbugger.  
Indeed, continuing the run normally would trigger a debugging attempt detection and cause the program to quit!

Let's just manually replace its response with a nice `0` in order to allow the program to continue!

```gdb
(gdb) break *0x804898e
Breakpoint 2 at 0x804898e
(gdb) step
Single stepping until exit from function main,
which has no line number information.

Breakpoint 2, 0x0804898e in main ()
(gdb) set $eax=0
```

Now let's see what's coming up.
```gdb
(gdb) disas
Dump of assembler code for function main:
   [...]
   0x08048989 <+67>:	call   0x8048540 <ptrace@plt>
=> 0x0804898e <+72>:	test   %eax,%eax
   0x08048990 <+74>:	jns    0x80489a8 <main+98>
   0x08048992 <+76>:	movl   $0x8048fa8,(%esp)
   0x08048999 <+83>:	call   0x80484e0 <puts@plt>
   0x0804899e <+88>:	mov    $0x1,%eax
   0x080489a3 <+93>:	jmp    0x8048eb2 <main+1388>
   0x080489a8 <+98>:	movl   $0x8048fc4,(%esp)
   0x080489af <+105>:	call   0x80484d0 <getenv@plt>
   [...]
   0x08048af5 <+431>:	mov    %eax,(%esp)
   0x08048af8 <+434>:	call   0x80484c0 <fwrite@plt>
   0x08048afd <+439>:	call   0x80484b0 <getuid@plt>
   0x08048b02 <+444>:	mov    %eax,0x18(%esp)
   0x08048b06 <+448>:	mov    0x18(%esp),%eax
   [...]

```
There's our call to `getuid`! 
Now let's just get at right after it, when the program goes back to main and does anything with the output of `getuid` (`eax`)
```
(gdb) break getuid
Breakpoint 3 at 0xb7ee4cc0
(gdb) step
Single stepping until exit from function main,
which has no line number information.

Breakpoint 3, 0xb7ee4cc0 in getuid ()
   from /lib/i386-linux-gnu/libc.so.6
(gdb) step
Single stepping until exit from function getuid,
which has no line number information.
0x08048b02 in main ()
(gdb) disas
   [...]
   0x08048afd <+439>:	call   0x80484b0 <getuid@plt>
=> 0x08048b02 <+444>:	mov    %eax,0x18(%esp)
   0x08048b06 <+448>:	mov    0x18(%esp),%eax
   [...]
```

We are where we want to be!   
Now we just need to replace the content of `eax` with the UID of user `flag14` and let the program finish.

A quick peek into `/etc/passwd` gives out that the `flag14` UID is 3014.

```gdb
(gdb) set $eax=3014
(gdb) step
Single stepping until exit from function main,
which has no line number information.
Check flag.Here is your token : 7QiHafiNa3HVozsaXkawuYrTstxbpABHD8CPnHJ
```

And we're finished!