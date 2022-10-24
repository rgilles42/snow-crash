# level08
We are greeted with an executable called `level08` and a file called `token`.  
`token` is owned by `flag08`, is read-write for `flag08` only and has zero permission for anyone else.  
`level08` is also owned by `flag08` but has its GID set to group `level08` and permissions of `rws r-s ---`.  
It also has ACLs but they don't really bring anything different from standard permissions.

Running the program, it expects us to pass it a "file to read".  
It indeed reads a random file created under /tmp.

However, it doesn't want to read the `token` file, even though it should have its access granted!  
Even more curiously, it prints out a laconical "You may not access this file", which is different from its output in both file not found and permission denied scenarii.

Looking at its strings, we can see that there's a `token` keyword hardcoded here.  
And when we create our own `token` file under `/tmp`, it also refuses to read it! Or really any file that starts with the name `token`.

# Resolution
We have to avoid the program detecting the file we are giving it to process is called `token`.

To do so, we will be creating a symbolic link to the `token` file ang giving it a totally different name.
```sh
ln -s /home/user/level08/token /tmp/link
```

And giving this link to the program prints us the token to access `flag08`!