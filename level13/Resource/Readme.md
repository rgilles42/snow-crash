# level13
We are greeted with a single compiled program called `level13`. It is setuid'd as flag13 but we can run it.  
When ran, it complains about being started by `UID 2013` and not `UID 4242`.

When running `nm level13`, we see that it uses the function `getuid` from the libc. Hijacking this function would most probably unlock things further.

# Resolution
Under `/tmp`, we will write a replacement for the libc function `getuid` and compile it as a shared library.
```sh
cd /tmp
echo "int getuid(){return(4242);}" > newgetuid.c
gcc -shared ./newgetuid.c -o getuid.so
```

We will then use the `LD_PRELOAD` to inject our new library on front of all the others.  
In theory, it will make `level13` use the `getuid` function from our very own library instead of the regular `libc` one.
```sh
LD_PRELOAD=/tmp/getuid.so /home/user/level13/level13
```
At first, it doesn't seem to change its behaviour!  
But when we run the program with gdb instead...
```sh
level13@SnowCrash:/tmp$ LD_PRELOAD=/tmp/getuid.so gdb /home/user/level13/level13
GNU gdb (Ubuntu/Linaro 7.4-2012.04-0ubuntu2.1) 7.4-2012.04
[...]
Reading symbols from /home/user/level13/level13...(no debugging symbols found)...done.
(gdb) run
Starting program: /home/user/level13/level13 
your token is 2A31L79asukciNyi8uppkEuSx
[Inferior 1 (process 20181) exited with code 050]
(gdb) 

```
