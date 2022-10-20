# level06

We are greeted with a php file called `level06.php` and an executable owned by user `flag06`.  
The php file itself has a shebang so that it could be executed directly, but would need its `argv` array provided.  
The executable is very short and the strings it contains hints at the fact it is just a wrapper for calling the php file as the `flag06` and passing its arguments.

Let's dig into the PHP file.  
A brief analysis shows that the program consists in reading from a file which path is passed as first argument, applying to it a bunch of regex replaces and then printing the result.  
However only the first `preg_replace` is really of interest:
- it takes any input in the form `[x <something>]` and replaces it with `y("<something>")`;
- it then tries to execute what it just replaced as a PHP statement, thanks to the (very dangerous and thankfully now deprecated) `/e` regex flag !

So this level is just a matter of injecting code to get our flag into this expression, which will then conveniently be run as user flag06.

# Resolution

Let's make our script execute some harmless program, such as `uname`.

We are going to create a file on `/tmp` as such:
```
[x hello{${exec(uname)}}]
```

We will be passing that file to the executable.

The intended behaviour is as such: 
- function `x` will be run, `$a` will be populated with our crafted string.
- the first `preg_replace` instruction will replace our string with `y("hello{${exec(uname)}}")`.
- The new string will then be executed: 
    - PHP will start trying to substitute the object that is located inside the curvy brackets, in this case `${exec(uname)}`
    - It will automatically put `uname` into single quotes since it's used within a function but doesn't correspond to any particular variable or keyword
    - It will execute the program `uname` as user `flag06`, which is a program that prints `Linux` on Linux. So `${exec(uname)}` is substituted with `Linux`
    - So now PHP will have to keep resoluting this string: `y("hello{Linux}")`, but "Linux" isn't anything PHP knows about
    - An error message is printed on STDOUT saying `PHP Notice:  Undefined variable: Linux in /home/user/level06/level06.php(4) : regexp code on line 1`
    - `{Linux}` is then substituted with nothing, it's just deleted from the string
    - There's nothing to substitute anymore, `y("hello")` is executed
- Nothing happens to `"hello"` until the end of the program because it matches none of the regex replaces.
- "hello" is finally printed.

Notice that at some point, the actual output of our shell command is printed raw on STDOUT in an error message.  
So Now, instead of `uname`, we just have to launch `getflag` and we've got our flag!
