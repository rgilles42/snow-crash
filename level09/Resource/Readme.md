# level09
We are greeted with an executable called `level09` and a file called `token`.  
`token` is owned by `flag09`, but is not accessible in any way for `flag09`, and only and has read permission for both group `level09` and others.  
`level09` is also owned by `flag09` but has its GID set to group `level09` and permissions of `rws r-s r-x`.  

`level09` is a program that is supposed to take one argument, and it doesn't seem to resolve a path.
Let's see what it does
```sh
level09@SnowCrash:~$ ./level09 aaaaa
abcde
```
It merely transforms the input by offsetting each character with its position in the string. It's Caesar all over again and a cool nod to the first level in order to end the mandatory part of this exercise  

But now what to do with the `token` file, which is just a small text file with some undisplayable characters?  
Well, it's guessable at this point that `token` would be the output of `level09` when the password for flag09 were to be input.

# Resolution
Let's write a program that simply applies the opposite operation to a string.  
The following perl program will do just that.
```perl
$index = 0;
@arr = unpack("C*", <>);
foreach my $char (@arr) {
	$char = $char - $index++;
}
print pack("C*", @arr), "\n"
```
It's now just a matter to run it properly:
```sh
level09@SnowCrash:~$ perl -e '$index = 0; @arr = unpack("C*", <>); foreach my $char (@arr) {$char = $char - $index++;} print pack("C*", @arr), "\n"' token
f3iji1ju5yuevaus41q1afiuq�
```
NB: The unreadable token is just the line break at the end of the line read with the diamond operator.
