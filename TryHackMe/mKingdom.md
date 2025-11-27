This was a good one, took around 2 hours to complete.
Got a port: 85 and got in as admin:password. Then appended some file extensions, got a reverse shell using a php exploit. Then got in as www-data and found did linpeas.sh.

Found an unauthenticated db, got a password but it was just admin:password, duh. 
Also on linpeas, I found a password which was b64 enc and got in as mario:ikaTeNTANtES.
Also found toad:toadisthebest. 
mario had some privileges and it was /usr/bin/id, quite unconventional.

So, I found something called https://github.com/DominicBreuker/pspy
and found a routine daemon that 
```
2025/08/09 22:41:01 CMD: UID=0     PID=4902   | /bin/sh -c curl mkingdom.thm:85/app/castle/application/counter.sh | bash >> /var/log/up.log  
```
then I tried appending the counter.sh but no privileges.
Then I just DNS poisoned it, hehehe. Made a server on my device and then made the counter.sh a rev shell to my local device. 

Surprisingly, got in as root, yay~
