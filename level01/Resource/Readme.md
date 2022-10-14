# level01
We are greeted, very much in the same fashion as the previous level, with nothing at hand.
No scan of the machine brings anything relevant, ownership-wise.

How could we ever obtain this direly needed password?
Do we even need a password ?

# Resolution
Let's head to the /etc/passwd file.
This file contains infox about all the users on the machine, their UIDs, GIDs, and also their password validation mechanism.
No regular user should be able to read it, since its content are quite sensitive.
But its permissions are set so that everyone can read it! 

On all users, the "x" on the second field of the string indicates that the password should be compared against a hash on a separate file, `/etc/shadow`, which we cannot access.

All users? No! Only one indomitable user still holds out against the common good practice of computer security.
And of course, this user is of the highest relevance for us.

`flag01:42hDRfypTqqnw:3001:3001::/home/flag/flag01:/bin/bash`

What that means is that the password hash of the user we need is directly embedded into a file we can read.

That means it's password bruteforcing time. We're gonna use John.

Let's fire up a debian container on our host.
```sh
docker run -it --name test debian /bin/bash
```
After installing ssh and john, and grabbing the /etc/passwd via scp, we can decode.
```sh
root@db5b1d8d1f39:~# john passwd 
Created directory: /root/.john
Loaded 1 password hash (descrypt, traditional crypt(3) [DES 128/128 SSE2-16])
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
abcdefg          (flag01)
1g 0:00:00:00 100% 2/3 2.631g/s 44752p/s 44752c/s 44752C/s 123456..betabeta
Use the "--show" option to display all of the cracked passwords reliably
Session completed
```

That was an easy one.