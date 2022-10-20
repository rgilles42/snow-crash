# level07

We are greeted with a single executable, belonging to flag07 and group `level07` with permissions as `rws r-s r-x`.  
This executable is binary code.

When ran, it outputs `level07`

Looking at the strings, some things catch our attention:
- a reference to function `getenv`, which gets environment variables as C strings.
- a reference to function `system`, which executes shell commands fro; within a C program
- the string `LOGNAME`, which is a standard UNIX environment variable meant to store the username of the current user, the static brother of the `logname` program, in our case `level07`
- the string `/bin/echo %s`

It is very plausible that this program just executes the command `/bin/echo %s` with `%s` being whatever the variable `LOGNAME` is.  
Let's first make sure.

Let's replace the content of `LOGNAME` with something else.
```sh
level07@SnowCrash:~$ LOGNAME="hello"
level07@SnowCrash:~$ ./level07 
hello
```
That's one point for us.

# Resolution

Now the point will be to make sure the program launches something else than just an `echo` command.  
Let's remember this program will run as user `flag07`

We can do it this way.
```sh
LOGNAME="coucou; getflag"
```

And as simple as that, we get our flag.