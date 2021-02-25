---
title: HTB - Ready
layout: post
---

#Ready
This one was short and sweet, but definitely made me realize I have a lot to learn about docker and kernel capabilities.

<br/>
<img src="../assets/img/htb/medium/Ready/nmap.png">
<br/>
Initail recon is initial recon.

<br/>
<img src="../assets/img/htb/medium/Ready/rce.png">
<br/>
I tried the original exploit, but had dependancy problems on my machine.  Spent 20 mins fixing them only to get indice out of bounds.  So this is a re-write of that same exploit.  Better luck?

<br/>
<img src="../assets/img/htb/medium/Ready/rce1.png">
<br/>
Here's what it needs.

<br/>
<img src="../assets/img/htb/medium/Ready/rce2.png">
<br/>
Succes?

<br/>
<img src="../assets/img/htb/medium/Ready/rce3.png">
<br/>
Success!

<br/>
<img src="../assets/img/htb/medium/Ready/capchwn.png">
<br/>
This can be used to chown files as if we had id=0.  Not sure exactley how yet..

<br/>
<img src="../assets/img/htb/medium/Ready/peasbeinghelpful.png">
<br/>
LinPEAS is a gift from god I swear.  I wish I caught this when I was doing Laboratory...

<br/>
<img src="../assets/img/htb/medium/Ready/adminpanel.png">
<br/>
Okay I'm an admin.  This was a lot easier than manipulating the DB like I did a certain other gitlab machine... I'm sick of gitlab.

<br/>
<img src="../assets/img/htb/medium/Ready/smtppass.png">
<br/>
Nothing in the user's repo on first look over.  Here's an password though.  Thanks linpeas!

<br/>
<img src="../assets/img/htb/medium/Ready/rootmaybe.png">
<br/>
Okay that password got us "root".

<br/>
<img src="../assets/img/htb/medium/Ready/docker.png">
<br/>
Hence the quotations.

<br/>
<img src="../assets/img/htb/medium/Ready/docker.png">
<br/>
Reading up on comon docker escapes, I learned about the --privelaged flag used to start container instances with privelages to access the hard disk directly.

<br/>
<img src="../assets/img/htb/medium/Ready/lsblk.png">
<br/>
Unfortunately it's not going to be that easy!

<br/>
<img src="../assets/img/htb/medium/Ready/containermount.png">
<br/>
Found a PoC on hacktricks that shows runs commands on the host.  Not sure the specifics of how it works unfortunately but I'm plannig on doing a deep dive on docker security at some point, probably on TryHackMe.
<br/>
<img src="../assets/img/htb/medium/Ready/root1.png">
<br/>
Here's the alterations I made to get the root ssh key.

<br/>
<img src="../assets/img/htb/medium/Ready/root2.png">
<br/>
Here's the root ssh key.

<br/>
<img src="../assets/img/htb/medium/Ready/root3.png">
<br/>
Done!
