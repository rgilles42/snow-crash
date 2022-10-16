# level03
We are greeted by a single file in our home folder. It's an executable called `level03`.

This file is owned by user flag03, which is very interesting. Its group ID corresponds to group `level03`, the main group of our current user of the same name.
Its permissions are `rws r-s r-x`. The s here stands for `setuid`, which is a special permission that allows the user to run an executable as the owner of the file with its effective UID.
This means that any user of the level03 group (including us) can run that program as if it was run by level03.

But what does this executable anyway?
When executed, it just prints the sentence `Exploit me` and returns.

But there's got to be more to it, right ?
Well, the approach is dirty, but if we just open the executable with vim to have a first look at the string content of the program (fortunately, that's a very short one), there's something that stands out.
```sh
/usr/bin/env echo Exploit me
```
This is a shell command that does exactly what happens when we run the program!
So we can assume that the program is just a glorified shell wrapper for using the `echo` program.

# echo
But first, some shenanigans about echo.
In all shells on the universe (even on our minishells) echo is a builtin.
That means that when you type `echo dog`, while you would expect a program called `echo` to be, well, called, and have `dog` passed to it as an argument, the shell itself has a bunch of functions to do that directly for you.
And that even though there probably really IS a stand-alone `echo` program somewhere on the computer.
```sh
level03@SnowCrash:~$ whereis echo
echo: /bin/echo /usr/share/man/man1/echo.1.gz
```
But what if you really wanted to use the stand-alone echo program available on your system instead of the shell builtin one?
Well, you could use the program `env`. While the `env` you probably know prints out the current environment variables, it's not its sole purpose. 

Indeed, it's main functionnality is to run programs in a different, alterable environment than the one your current shell is giving.
This is a proper way to do the same thing as above but by getting rid of the shell builtin.
```sh
env echo dog
```

# Resolution
So the program we have uses the stand-alone, environment provided `echo` to print text.
It uses the `PATH` environment variable to find the program named `echo` by browsing the different directories the variable contains.
By default, `PATH` is as such :
```sh
level03@SnowCrash:~$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games
```
The first program called `echo` encountered while browsing all these directories will be the one to be run, here directly as if it were run by flag03.

So, what if we made our own `echo` program, one that would just call `getflag`, placed it in some directory and have that directory be first on the `PATH` dirsctory listing?

We are creating an `echo` file under /tmp, with `755` permissions, and containing the following
```
#!/bin/sh
getflag
```

Then we are adding /tmp at the beginning of the `PATH` variable.
```sh
$ PATH=/tmp:$PATH
```

And now, when `level03` is ran again, we get what we want! 