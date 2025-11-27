This was a new one, only one port was there. So, probably a web to RCE kind of CTF.

Then got some directories and also one flag, which turned out to be a cookie for the site to access the main site. Did that and got some images ran steghide and exiftools, no game.

Then on the hint, there was written that we may use other HTTP verbs, most probable ones were POST and OPTIONS. 
Parameter fuzzed most endpoints and got something on the /api/items?fuzz=FUZZ one.

Then used node.js rev shell commands from revshells but no luck. Then tried it using nc mkfifo and it actually worked?

Then got in as `user` and we could not run linpeas.sh. 
Manually enumerated the machine, got a flag, also got a sus .firefox folder it had sqlite db and some creds, cracked it using https://github.com/unode/firefox_decrypt. 

Then got in as `v0id` and switched to root using `doas`. 
https://exploit-notes.hdks.org/exploit/linux/privilege-escalation/doas/
done~