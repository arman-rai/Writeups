Description:

Test your enumeration skills on this boot-to-root machine.

`Pyrat receives a curious response from an HTTP server, which leads to a potential Python code execution vulnerability. With a cleverly crafted payload, it is possible to gain a shell on the machine. Delving into the directories, the author uncovers a well-known folder that provides a user with access to credentials. A subsequent exploration yields valuable insights into the application's older version. Exploring possible endpoints using a custom script, the user can discover a special endpoint and ingeniously expand their exploration by fuzzing passwords. The script unveils a password, ultimately granting access to the root.`

HTTP server hosted on port 8000 + ssh p22

Fuzz garda sabai directory ma chai response chai aairaxa

sabai ma dherai jaso response chai aaudai xa

no exploit db seen

LFI doesn’t work

tait

i tried using burp to intercept and also wireshark to view the connections

interesting directories ma sabai check gare

?eval= ra ?input try gari hiday

tait

last ma ta nc po hanna parne raixa

tyai lekhya raixa

hawa

after 1 ghanta of trying

```jsx
www-data@ip-10-10-40-254:/var/opt$ cat /opt/dev/.git/config
cat /opt/dev/.git/config
[core]
        repositoryformatversion = 0
        filemode = true
        bare = false
        logallrefupdates = true
[user]
        name = Jose Mario
        email = josemlwdf@github.com

[credential]
        helper = cache --timeout=3600

[credential "<https://github.com>"]
        username = think
        password = _TH1NKINGPirate$_

```

[https://www.exploit-db.com/exploits/50011](https://www.exploit-db.com/exploits/50011)

[https://gtfobins.github.io/#+sudo](https://gtfobins.github.io/#+sudo)

[https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#sudo-version](https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#sudo-version)

Yesma herna parne raixa

[https://github.com/josemlwdf/PyRAT/blob/main/pyrat.py](https://github.com/josemlwdf/PyRAT/blob/main/pyrat.py)