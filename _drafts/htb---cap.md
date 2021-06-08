---
title: HTB - Cap
layout: 
category: writeup
---

<br/>
<img src="../assets/img/htb/easy/Cap/title-card.png">
<br/>

# Cap
Intro intro intro....

## Initial Recon
Initial foothold took a second... Dig around the site properly and you'll find a few pcaps, one of which contains the user credentials.

<br/>
<img src="../assets/img/htb/easy/Cap/ftp-creds.png">
<br/>

<br/>
<img src="../assets/img/htb/easy/Cap/ftp.png">
<br/>

<br/>
<img src="../assets/img/htb/easy/Cap/ssh.png">
<br/>

## The Linpeas Game
My methodology revolves entirely around linpeas.  While it runs, I will sit here in shame at my lack of manual privesc enumeration skills..........
<br/>

This looks promising.
<br/>
<img src="../assets/img/htb/easy/Cap/cap-setuid.png">
<br/>

A little too easy...
<br/>
<img src="../assets/img/htb/easy/Cap/root.png">
<br/>