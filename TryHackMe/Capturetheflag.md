```jsx
┌──(namura㉿namu)-[~/thm/c4ptur3th3fl4g]
└─$ exiftool secretaudio_1559007588454.wav
```

audacity bata bhayo

```jsx
┌──(namura㉿namu)-[~/thm/c4ptur3th3fl4g]
└─$ ls
output.png  secretaudio_1559007588454.wav  stegosteg_1559008553457.jpg
┌──(namura㉿namu)-[~/thm/c4ptur3th3fl4g]
└─$ steghide extract -sf stegosteg_1559008553457.jpg 
Enter passphrase: 
steghide: could not extract any data with that passphrase!

└─$ strings stegosteg_1559008553457.jpg 

┌──(namura㉿namu)-[~/thm/c4ptur3th3fl4g]
└─$ steghide extract -sf stegosteg_1559008553457.jpg
Enter passphrase: 
wrote extracted data to "steganopayload2248.txt".

password was blank
┌──(namura㉿namu)-[~/thm/c4ptur3th3fl4g]
└─$ ls
output.png  secretaudio_1559007588454.wav  steganopayload2248.txt  stegosteg_1559008553457.jpg
                                                                                                                                                              
┌──(namura㉿namu)-[~/thm/c4ptur3th3fl4g]
└─$ cat steganopayload2248.txt 
SpaghettiSteg     
```

```jsx
┌──(namura㉿namu)-[~/thm/c4ptur3th3fl4g]
└─$ stat meme_1559010886025.jpg                     
  File: meme_1559010886025.jpg
  Size: 80123           Blocks: 160        IO Block: 4096   regular file
Device: 259,6   Inode: 1314822     Links: 1
Access: (0664/-rw-rw-r--)  Uid: ( 1000/  namura)   Gid: ( 1000/  namura)
Access: 2025-07-04 21:15:14.787575911 +0545
Modify: 2025-07-04 21:14:21.820286553 +0545
Change: 2025-07-04 21:14:24.156253553 +0545
 Birth: 2025-07-04 21:14:21.816286609 +0545
                                                                                                                                                              
┌──(namura㉿namu)-[~/thm/c4ptur3th3fl4g]
└─$ binwalk meme_1559010886025.jpg 

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             JPEG image data, JFIF standard 1.01
30            0x1E            TIFF image data, big-endian, offset of first image directory: 8
74407         0x122A7         RAR archive data, version 5.x
74478         0x122EE         PNG image, 147 x 37, 8-bit/color RGBA, non-interlaced
74629         0x12385         Zlib compressed data, default compression

                                                                                                                                                              
┌──(namura㉿namu)-[~/thm/c4ptur3th3fl4g]
└─$ binwalk -e meme_1559010886025.jpg

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
74407         0x122A7         RAR archive data, version 5.x
74629         0x12385         Zlib compressed data, default compression

WARNING: One or more files failed to extract: either no utility was found or it's unimplemented

                                                                                                                                                              
┌──(namura㉿namu)-[~/thm/c4ptur3th3fl4g]
└─$ ls    
meme_1559010886025.jpg  _meme_1559010886025.jpg.extracted  output.png  secretaudio_1559007588454.wav  steganopayload2248.txt  stegosteg_1559008553457.jpg
                                                                                                                                                              
┌──(namura㉿namu)-[~/thm/c4ptur3th3fl4g]
└─$ cd _meme_1559010886025.jpg.extracted 
                                                                                                                                                              
┌──(namura㉿namu)-[~/thm/c4ptur3th3fl4g/_meme_1559010886025.jpg.extracted]
└─$ ls
122A7.rar  12385  12385.zlib  hackerchat.png
                                                                                                                                                              
┌──(namura㉿namu)-[~/thm/c4ptur3th3fl4g/_meme_1559010886025.jpg.extracted]
└─$ 
                                                                                                                                                              
┌──(namura㉿namu)-[~/thm/c4ptur3th3fl4g/_meme_1559010886025.jpg.extracted]
└─$ unzip 122A7.rar 
Archive:  122A7.rar
  End-of-central-directory signature not found.  Either this file is not
  a zipfile, or it constitutes one disk of a multi-part archive.  In the
  latter case the central directory and zipfile comment will be found on
  the last disk(s) of this archive.
unzip:  cannot find zipfile directory in one of 122A7.rar or
        122A7.rar.zip, and cannot find 122A7.rar.ZIP, period.
                                                                                                                                                              
┌──(namura㉿namu)-[~/thm/c4ptur3th3fl4g/_meme_1559010886025.jpg.extracted]
└─$ tar -xvf 122A7.rar
tar: This does not look like a tar archive
tar: Skipping to next header
tar: Exiting with failure status due to previous errors
                                                                                                                                                              
┌──(namura㉿namu)-[~/thm/c4ptur3th3fl4g/_meme_1559010886025.jpg.extracted]
└─$ unrar e 122A7.rar 

UNRAR 7.11 freeware      Copyright (c) 1993-2025 Alexander Roshal

Extracting from 122A7.rar

Would you like to replace the existing file hackerchat.png
  5562 bytes, modified on 2019-05-28 08:15
with a new one
  5562 bytes, modified on 2019-05-28 08:15

[Y]es, [N]o, [A]ll, n[E]ver, [R]ename, [Q]uit Y

Extracting  hackerchat.png                                            OK 
All OK
                                                                                                                                                              
┌──(namura㉿namu)-[~/thm/c4ptur3th3fl4g/_meme_1559010886025.jpg.extracted]
└─$ ls
122A7.rar  12385  12385.zlib  hackerchat.png
                                                  
```

```jsx
┌──(namura㉿namu)-[~/thm/c4ptur3th3fl4g/_meme_1559010886025.jpg.extracted]
└─$ binwalk hackerchat.png 

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             PNG image, 147 x 37, 8-bit/color RGBA, non-interlaced
151           0x97            Zlib compressed data, default compression

                                                                                                                                                              
┌──(namura㉿namu)-[~/thm/c4ptur3th3fl4g/_meme_1559010886025.jpg.extracted]
└─$ binwalk -e hackerchat.png

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
151           0x97            Zlib compressed data, default compression

WARNING: One or more files failed to extract: either no utility was found or it's unimplemented

┌──(namura㉿namu)-[~/thm/c4ptur3th3fl4g/_meme_1559010886025.jpg.extracted]
└─$ strings hackerchat.png 
```