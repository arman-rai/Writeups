Got reverse shell (python) using https://www.revshells.com/ as www-data
enumerate using https://github.com/peass-ng/PEASS-ng/tree/master/linPEAS

It had polkit:CVE-2021-3560 but it didn't work on my machine

on enumeration, I found `/home/apaar/.helpline.sh` with suid bit
then found out that I can masquerade as apaar so I asked for a bash shell as apar
`sudo -u apar /home/apaar/.helpline.sh`

then got in as apar and found the user flag
then enumerated further, there was `/var/www/files/images` and downloaded it to local machine using [[Obsidian/Tools/python]]

then used few tools, [[steghide]] got the job done
used [[zip2john]] to crack the zip file and got in
found password encoded as b64 on the unzipped file 

then tried to su to anurodh from apaar, succeded
then found a [[docker]] id on it and then used it to get the fuck outa the shell
`docker run -v /:/mnt --rm -it alpine chroot /mnt sh`

done~