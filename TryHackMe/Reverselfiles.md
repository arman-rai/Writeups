ltrace, strace

ghidra, gdb

Suru suru ko ta maile chmod haru diye ani

```jsx
┌──(namura㉿namu)-[~/thm/reverselfiles]
└─$ chmod +x crackme1 
                                                                                                                                                              
┌──(namura㉿namu)-[~/thm/reverselfiles]
└─$ ./crackme1       
flag{not_that_kind_of_elf}
                            
```

```jsx
┌──(namura㉿namu)-[~/thm/reverselfiles]
└─$ file crackme2 
crackme2: ELF 32-bit LSB executable, Intel i386, version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux.so.2, for GNU/Linux 2.6.32, BuildID[sha1]=b799eb348f3df15f6b08b3c37f8feb269a60aba7, not stripped
                                                                                                                                                              
┌──(namura㉿namu)-[~/thm/reverselfiles]
└─$ chmod +x crackme2 
                                                                                                                                                              
┌──(namura㉿namu)-[~/thm/reverselfiles]
└─$ ./crackme2       
Usage: ./crackme2 password
                                                                                                                                                              
┌──(namura㉿namu)-[~/thm/reverselfiles]
└─$ ./crackme2 super_secret_password
Access granted.
flag{if_i_submit_this_flag_then_i_will_get_points}

```

yo super secret passwd chai question mai thiyo so tei handeko

3 ma pani testai try gare ani strings hane

```jsx
                                                                                                                                                              
┌──(namura㉿namu)-[~/thm/reverselfiles]
└─$ strings crackme3 
```

euta base64 wala bhete

```jsx
┌──(namura㉿namu)-[~/thm/reverselfiles]
└─$ echo "ZjByX3kwdXJfNWVjMG5kX2xlNTVvbl91bmJhc2U2NF80bGxfN2gzXzdoMW5nNQ==" | base64 -d 
f0r_y0ur_5ec0nd_le55on_unbase64_4ll_7h3_7h1ng5      
```

4 ko pani testai try gare

```jsx
┌──(namura㉿namu)-[~/thm/reverselfiles]
└─$ ./crackme4 
Usage : ./crackme4 password
This time the string is hidden and we used strcmp
                                                                                                                                                              
┌──(namura㉿namu)-[~/thm/reverselfiles]
└─$ binwalk crackme4 

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             ELF, 64-bit LSB executable, AMD x86-64, version 1 (SYSV)

                                                                                                                                                              
┌──(namura㉿namu)-[~/thm/reverselfiles]
└─$ strings crackme4 

```

```jsx
┌──(namura㉿namu)-[~/thm/reverselfiles]
└─$ objdump -s crackme4
┌──(namura㉿namu)-[~/thm/reverselfiles]
└─$ xxd crackme4| less
┌──(namura㉿namu)-[~/thm/reverselfiles]
└─$ ltrace ./crackme4 
__libc_start_main(["./crackme4"] <unfinished ...>
printf("Usage : %s password\\nThis time th"..., "./crackme4"Usage : ./crackme4 password
This time the string is hidden and we used strcmp
)                                     = 78
+++ exited (status 0) +++
                                                                                                                                                              
┌──(namura㉿namu)-[~/thm/reverselfiles]
└─$ strace ./crackme4 
┌──(namura㉿namu)-[~/thm/reverselfiles]
└─$ objdump -D ./crackme4 >crackme4.asm
                                                                                                                                                              
┌──(namura㉿namu)-[~/thm/reverselfiles]
└─$ open crackme4.asm 
┌──(namura㉿namu)-[~/thm/reverselfiles]
└─$ ltrace ./crackme4 hello
__libc_start_main(["./crackme4", "hello"] <unfinished ...>
strcmp("my_m0r3_secur3_pwd", "hello")                                                            = 5
printf("password "%s" not OK\\n", "hello"password "hello" not OK
)                                                        = 24
+++ exited (status 0) +++
```

```jsx
┌──(namura㉿namu)-[~/thm/reverselfiles]
└─$ ltrace ./crackme5 strncmp
__libc_start_main(["./crackme5", "strncmp"] <unfinished ...>
puts("Enter your input:"Enter your input:
)                                                                        = 18
__isoc99_scanf(0x400966, 0x7ffedd97c480, 0, 0
strncmp
)                                                   = 1
strlen("strncmp")                                                                                = 7
strlen("strncmp")                                                                                = 7
strlen("strncmp")                                                                                = 7
strlen("strncmp")                                                                                = 7
strlen("strncmp")                                                                                = 7
strlen("strncmp")                                                                                = 7
strlen("strncmp")                                                                                = 7
strlen("strncmp")                                                                                = 7
strncmp("strncmp", "OfdlDSA|3tXb32~X3tX@sX`4tXtz\\331\\177", 28)                                   = 36
puts("Always dig deeper"Always dig deeper
)                                                                        = 18
+++ exited (status 0) +++

```

gdb ra r2 use gare ani try gareko thiye

```jsx
┌──(namura㉿namu)-[~/thm/reverselfiles]
└─$ chmod +x crackme6 
                                                                                                                                                              
┌──(namura㉿namu)-[~/thm/reverselfiles]
└─$ ./crackme6 
Usage : ./crackme6 password
Good luck, read the source
                                                                                                                                                              
┌──(namura㉿namu)-[~/thm/reverselfiles]
└─$ ./crackme6 password
password "password" not OK
                                                                                                                                                              
┌──(namura㉿namu)-[~/thm/reverselfiles]
└─$ objdump -D ./crackme6 > crackme6.asm
                                                                                                                                                              
┌──(namura㉿namu)-[~/thm/reverselfiles]
└─$ open crackme6.asm 
                                                                                                                                                              
┌──(namura㉿namu)-[~/thm/reverselfiles]
└─$ ltrace ./crackme6      
__libc_start_main(["./crackme6"] <unfinished ...>
printf("Usage : %s password\\nGood luck, r"..., "./crackme6"Usage : ./crackme6 password
Good luck, read the source
)                                     = 55
+++ exited (status 0) +++
                   
                   read the src bhandai thiyo so testai gare                                         
```

gdb ma suru ma ta run garnai paryo

ani tespaxi break points set gare main ra tyo function ma

ani breakpoint hane function ra registers ma

```jsx
(gdb) b main
Breakpoint 1 at 0x400715
(gdb) r
Starting program: /home/namura/thm/reverselfiles/crackme6 
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".

Breakpoint 1, 0x0000000000400715 in main ()
(gdb) info functions

(gdb) b * 0x000000000040057d 
Breakpoint 2 at 0x40057d
(gdb) r
(gdb) info registers

bhako breakpoint ma
(gdb) x/s 0x000000000040057d
0x40057d <my_secure_test>:      "UH\\211\\345H\\211}\\370H\\213E\\370\\017\\266"
(gdb) disas 0x40057d

yesmai ans raixa bhanne dekhe 

```

ghidra install gare ani yo my_secure_test function ma gaye ani bhate

7 ma pani testai gare

ltrace strace running the binary, gdb etc haru hane

ghidra mai gaye

tesma main function gare ani reverse gare

tetti nai raixa

easy tara 0 knowledge bhako ma ta tanab