# level00
After a tedious VM setup, I landed in the first session of my journey.
In the home folder of level00, specifically.

Alas! An `ls -lah` shows strictly nothing. I shall start like I imagine I'll end: clueless and alone.

# Resolution
Fine! If there's nothing relevant to my interest at hand, I'll scan the whole damn thing for any files I could own.

`find / -path /proc -prune -o -user user00 -print 2>/dev/null`
I run the find command on /, looking for files which are owned by flag00 (bear the flag00 uid), but which are not in the /proc directory, while sending any error on STDERR (the irrelevant "Permission denied" messages) to oblivion.

But nothing comes up !
Thats's when I had the idea repeat the same process, but instead of looking for my own files, I'd look for files which belong to the flag00 user, which I'm trying to connect as.

`find / -path /proc -prune -o -user flag00 -print 2>/dev/null`

Hurray!
Two files come up !
```sh
/usr/sbin/john
/rofs/usr/sbin/john
```
It's actually one file, rofs is a mirror to / related to the fact that we're booted up on a read-only ISO image.

But what can we do with it, as level00?
Well,
```sh
level00@SnowCrash:~$ ls -l /usr/sbin/john 
----r--r-- 1 flag00 flag00 15 Mar  5  2016 /usr/sbin/john
```
We can read it !

```sh
level00@SnowCrash:~$ cat /usr/sbin/john 
cdiiddwpgswtgt

```

That's funny because john is a famous password brute-force decryption program.
But this is no program at all and we got heckin' bamboozled.

Let's try to use that as the password for flag00.
```sh
level00@SnowCrash:~$ su flag00
Password: 
su: Authentication failure
```

That's not it...
Though, looking closer at the content of the file, the shortness of the content, the fact that it only contains alphabetical characters and the repetition of letters should shout "Cesar Code!" at any half talented puzzle enthusiast.

And sure enough, with a rotation of 15, we get:
`nottoohardhere`

And sure enough, with that as our password for flag00:
```sh
level00@SnowCrash:~$ su flag00
Password: 
Don't forget to launch getflag !
flag00@SnowCrash:~$ 
``` 