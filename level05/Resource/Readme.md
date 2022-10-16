# level05
In the same fashion as `level00`, there's nothing to welcome us in the home directory of user `level05`.

However, a quick
```sh
find / -path /proc -prune -o -user flag05 -print 2>/dev/null
```
returns us one file!

It's the executable `/usr/sbin/openarenaserver` owned by user `flag05`.

It's a bash script that would execute any bash script located in `/opt/openarenaserver` and then immediately delete it.

# Resolution
At this point, judging by the very naive logic of the program (depositing scripts somewhere to be run then cleaned up automatically...) and the inability to impersonate level05 and run it, I was confident that this script was run regularly by a CRON job.

And when we look at the mail for user `level05`, which has been the only user to ever have mail in `/var/mail`, there's a line that furiously resembles a cron entry that would run this very executable every two minutes...

Let's put a script at the designated place, have it print getflag somewhere and make it executable by everyone:
```sh
echo "getflag > /tmp/flag05" > /opt/openarenaserver/test.bash; chmod 777 /opt/openarenaserver/test.bash
```
And now let's just wait for a bit and see if something happens...

And just a few dozens of seconds later, our script has magically vanished from the designated location, and our fresh flag is waiting for us in `/tmp/flag05`