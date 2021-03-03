---
title: HTB - Laboratory
layout: post
category: writeup
---

#Laboratory
Definitely not an easy box for me.  Ready and Passage were both easier in my oppinion.  The Arbitrary File Read turned RCE was  alot of fun to read about and try out though. 

<br/>
<img src="../assets/img/htb/easy/Laboratory/nmap.png">
<br/>
Initail recon is initial recon.

<br/>
<img src="../assets/img/htb/easy/Laboratory/etchosts.png">
<br/>
Navigating to the website, it's clear we have to add this to to /etc/hosts/.

<br/>
<img src="../assets/img/htb/easy/Laboratory/usersmaybe.png">
<br/>
Potential user accounts on the box.

<br/>
<img src="../assets/img/htb/easy/Laboratory/nmap.png">
<br/>
Looking at the SSL cert I noticed something interesting.  I realized this was the case when I tried gobustering the https version of the site.

<br/>
<img src="../assets/img/htb/easy/Laboratory/githosts.png">
<br/>
Added the virtual host domain to /etc/hosts.

<br/>
<img src="../assets/img/htb/easy/Laboratory/gitlab.png">
<br/>
Okay.  Gitlab.

<br/>
<img src="../assets/img/htb/easy/Laboratory/register.png">
<br/>
Let's register for an account.

<br/>
<img src="../assets/img/htb/easy/Laboratory/emaildoamin.png">
<br/>
Okay...

<br/>
<img src="../assets/img/htb/easy/Laboratory/email.png">
<br/>
Okay that checks out.

<br/>
<img src="../assets/img/htb/easy/Laboratory/gitlabversion.png">
<br/>
Gitlab version is 12.8.1.

<br/>
<img src="../assets/img/htb/easy/Laboratory/gitlabfilereadrce.png">
<br/>
This looks like money.

<br/>
<img src="../assets/img/htb/easy/Laboratory/msf1.png">
<br/>
Heres the msf module.  Unfortunately the code that interacts with the gitlab API doesn't work, and I don't know ruby. :upside_down_face:

<br/>
After some nudging on the HTB Discord, I managed to find the original HackerOne disclosure thread about this bug. It explains how to pull off the arbitrary file read, as well as how to turn that into an RCE.  [Here's the disclosure.](https://hackerone.com/reports/827052)

<br/>
<img src="../assets/img/htb/easy/Laboratory/AFR1.png">
<br/>
Here is the payload to read the secrets.yaml file needed for RCE.

<br/>
<img src="../assets/img/htb/easy/Laboratory/AFR2.png">
<br/>
Once the issue is moved, we can download the file specified.

<br/>
I forgot to grab a screenshot of the process, but the msf module from earlier managed to execute the RCE when I set the secret_key_base to that of the server.

<br/>
<img src="../assets/img/htb/easy/Laboratory/rev.png">
<br/>
I grabed a proper tcp reverse shell because the msf payload one was giving me problems.  For some reason I could not change directories.  Either way a full TTY is easier to work in.  I'm sure there's a way to upgrade the reverse tcp msf payload but I am not a msf master quite yet.

<br/>
<img src="../assets/img/htb/easy/Laboratory/rails-console.png">
<br/>
Reading up on gitlab documentation, I am starting to get a feel for how the user path is going to go.  Here, I am testing the functionality of this rails console command.  The default storage behavior of gitlab is to hash project storage locations, instead of just storing in human readable locations on disk.  I am hoping I can guess dexter's project name? Not sure yet.


<br/>
<img src="../assets/img/htb/easy/Laboratory/database.png">
<br/>
Gave up on the rails console for now and went back to standard enumeration. Found this interesting config file.

<br/>
<img src="../assets/img/htb/easy/Laboratory/psql1.png">
<br/>
Alright this is progress.  This is NOT an easy machine IMO.

<br/>
Doing a `\dt` on the databse shows some possibly usefull tables: members, project_repositories, projects, users.

<br/>
Describing the users table with `\d users` shows these relevant collumns: id, username, encrypted_password, admin, 

<br/>
<img src="../assets/img/htb/easy/Laboratory/nix-dump.png">
<br/>
Okay so we can dump hashes.  Who knows if they're crackable yet.

<br/>
<img src="../assets/img/htb/easy/Laboratory/dex-dump.png">
<br/>
And here's dexters dump. Let's enum all users here just to see.  We still need user flag and root flag.

<br/>
<img src="../assets/img/htb/easy/Laboratory/admin1.png">
<br/>
<img src="../assets/img/htb/easy/Laboratory/admin2.png">
<br/>
Let's see what I can do as an admin.

<br/>
<img src="../assets/img/htb/easy/Laboratory/securedocker.png">
<br/>
I had a strong feeling I was in a container...

<br/>
<img src="../assets/img/htb/easy/Laboratory/dockerscript.png">
<br/>
This is the shell script used to bring up the gitlab container.

<br/>
<img src="../assets/img/htb/easy/Laboratory/ssh-key.png">
<br/>
Inside the secure docker project is dexter's private ssh key!

<br/>
<img src="../assets/img/htb/easy/Laboratory/dextershell.png">
<br/>
I'm super excited to finaly get user you have no idea! I have been working on this ALL day. :grin:

<br/>
<img src="../assets/img/htb/easy/Laboratory/docker-sec.png">
<br/>
Okay I was stuck here for too long.  Missed this line in linpeas.  Need to just break down and get a proper find methedology.

<br/>
<img src="../assets/img/htb/easy/Laboratory/docker-sec2.png">
<br/>
It's an ELF binary.  Strings is not installed on the machine.

<br/>
<img src="../assets/img/htb/easy/Laboratory/ltrace.png">
<br/>
Dynamic analysis of the binary shows what it's doing.  Namely, it's calling chmod from a relative path.

<br/>
<img src="../assets/img/htb/easy/Laboratory/privesc.png">
<br/>
Standard stuff here.

<br/>
<img src="../assets/img/htb/easy/Laboratory/root.png">
<br/>
I am so happy to be done with this box forever...

