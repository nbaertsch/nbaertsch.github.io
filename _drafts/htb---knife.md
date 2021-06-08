---
title: HTB - Knife
layout: 
category: writeup
---

<br/>
<img src="../assets/img/htb/medium/Armageddon/title-card.png">
<br/>

# Knife
Intro intro intro....

## Initial Recon
Google Zerodium.  I forgot to write this up while getting user, but that's all there is to it really.
<br/>
<img src="../assets/img/htb/easy/Knife/reverse-shell-1.png">
<br/>

<br/>
<img src="../assets/img/htb/easy/Knife/reverse-shell-2.png">
<br/>

<br/>
<img src="../assets/img/htb/easy/Knife/user.png">
<br/>

## Privilege Escalation
Linpeas says......
<br/>
<img src="../assets/img/htb/easy/Knife/sudo-version.png">
<br/>

This is exciting, I finally get to use that shiny "new" (not really anymore) sudo heap overflow.
<br/>
[POC here](https://github.com/CptGibbon/CVE-2021-3156).
<br/>

Okay, I should have known. It's patched :(
<br/>

But I found this.  I'm always slightly over-thinking these easy boxes....
<br/>
<img src="../assets/img/htb/easy/Knife/sudo-nopass.png">
<br/>

And we root bois!
<br/>
<img src="../assets/img/htb/easy/Knife/root.png">
<br/>

Short and sweet!