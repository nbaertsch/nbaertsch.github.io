---
title: HTB - Spectra
layout: 
category: writeup
---

# Spectra
This is a ChromeOS box, which from my understanding is based on Gentoo.  I am going to regret doing this probably.  (Spoiler alert, I didn't!)

<br/>
<img src="../assets/img/htb/medium/Passage/nmap.png">
<br/>
Initial nmap scan shows mysql, ssh, and nginx.

<br/>
<img src="../assets/img/htb/medium/Passage/wp-maybe.png">
<br/>
Wordpress?

<br/>
<img src="../assets/img/htb/medium/Passage/wp-vers.png">
<br/>
<img src="../assets/img/htb/medium/Passage/wp-users.png">
<br/>
This turns into a rabbit hole.  Password was not guessable.

<br/>
<img src="../assets/img/htb/medium/Passage/wp-save.png">
<br/>
Heres something interesting.

<br/>
<img src="../assets/img/htb/medium/Passage/mysql-creds.png">
<br/>
Viewing the source of that page shows the MySQL creds.

<br/>
<img src="../assets/img/htb/medium/Passage/db-denied.png">
<br/>
Well that looks like it isn't going to work.

<br/>
<img src="../assets/img/htb/medium/Passage/wp-admin.png">
<br/>
Password reuse.

<br/>
<img src="../assets/img/htb/medium/Passage/meterpreter.png">
<br/>
Admin access gets an easy shell upload.

<br/>
<img src="../assets/img/htb/medium/Passage/katie.png">
<br/>
Reading `/etc/passwd` shows us the user we will likely be trying to get.a[]

<br/>
<img src="../assets/img/htb/medium/Passage/db-creds.png">
<br/>
These are the real db creds for the live site.

<br/>
<img src="../assets/img/htb/medium/Passage/my-sql1.png">
<br/>
We can succesfully get into the MySQL DB, but it's kinda shitty without a tty.

<br/>
<img src="../assets/img/htb/medium/Passage/autologin.png">
<br/>
After spending a lot of time on nothing, I got hint from the discord to look in /opt

<br/>
<img src="../assets/img/htb/medium/Passage/passwd1.png">
<br/>
<img src="../assets/img/htb/medium/Passage/passwd2.png">
<br/>
Not sure who that password is for, I mean it can't really be root right.

<br/>
<img src="../assets/img/htb/medium/Passage/userflag.png">
<br/>
As expected, this is katie's password.  We can aslo see we are part of the developer group!

<br/>
<img src="../assets/img/htb/medium/Passage/init-test.png">
<br/>
This is definitely something important for privesc.  I looked on the internet to learn more about wha these are.  Read [here](https://askubuntu.com/questions/5039/what-is-the-difference-between-etc-init-and-etc-init-d). 

<br/>
<img src="../assets/img/htb/medium/Passage/orig-init.png">
<br/>
So this is on of the original init files.  These are all owned by root.  So we should be able to edit them, and then (somehow) have them run as root? I found [this](http://upstart.ubuntu.com/getting-started.html) getting started guide on Upstart (the init system used in place of SysVinit).

<br/>
<img src="../assets/img/htb/medium/Passage/sudo-l.png">
<br/>
That's convenient.  We're on the right track clearly.  I have noticed during this time that the test.conf file is getting overwritten periodically by what I'm guessing is a cron job.. Annoying.  

<br/>
<img src="../assets/img/htb/medium/Passage/test-root.png">
<br/>
I had problems running this quick enough so I wrote it to katie's home dir so I wouldnt have to keep re writing the job config.

<br/>
<img src="../assets/img/htb/medium/Passage/rootflag.png">
<br/>
Here you can see that the test job starts and actuall does what I want it to this time.
