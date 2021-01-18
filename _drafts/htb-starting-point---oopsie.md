---
title: HTB Starting Point - Oopsie
layout: post
categroy: writeup
---

# Oopsie
This box was a lot of fun to do because of how easy it was, and linux machines are just easier to wrap my brain in general.  There is a real sense of accomplishment from rooting a box without being hand-held through it.  I will concede that I looked at the walkthrough once because I didn't think to reuse passwords from the first box.  I also really enjoyed the web-app part of the challenge despite fumbling around the login page for way to long.  Enjoy this thorough walkthrough!


Initial recon.
<img src="../assets/img/htb/startingpoint/Oopsie/nmap.png">


Gobusted.  Everything is forbidden...
<img src="../assets/img/htb/startingpoint/Oopsie/gobuster1.png">


Zap spider didn't come up with much other than /cdn-cgi/login/script.js, but as you can see below it is an empty file.
<img src="../assets/img/htb/startingpoint/Oopsie/zap1.png">


Manually trying things that exepctedly don't work.
<img src="../assets/img/htb/startingpoint/Oopsie/login1.png">


This is an interesting find which is a great example of why you need to understand what your tools do and mannually check things.  Burp and Zap spidering only follow links.  Obviously the empty script.js file in this directory was called in the index as a clue as to where the login page would be, but there are no direct links to this page anywhere on the main page.
<img src="../assets/img/htb/startingpoint/Oopsie/login2.png">


I really like Zap because of the HUD integration in the browser. It allows you to intercept and modify requests without leaving the browser.
<img src="../assets/img/htb/startingpoint/Oopsie/zaphud.png">


This will most likely not work...
<img src="../assets/img/htb/startingpoint/Oopsie/badsqli.png">


At this point I broke down and looked at the walkthrough just for this step and.... it's a reused cred from Archetype.  I guess thats a thing we do here.  Megacorp refferences on this site I guess so I should have made the connection.
<img src="../assets/img/htb/startingpoint/Oopsie/rms.png">


Just poking around.  This looks like I can enumerate all user accounts.
<img src="../assets/img/htb/startingpoint/Oopsie/potentialsqli.png">


This is also interesting. Super admin rights?
<img src="../assets/img/htb/startingpoint/Oopsie/super-admin-req.png">


Preparing to fuzz user accounts. I tried manual incrementing and realized id's are not sequential. Or maybe there are no other users? Doubtfull.  I want to find this supposed super admin.
<img src="../assets/img/htb/startingpoint/Oopsie/preparing-to-fuzz.png">


Careless fuzzing.  This could take way to long...
<img src="../assets/img/htb/startingpoint/Oopsie/careless-fuzzing.png">


I stopped fuzzing at around 4%...


Sorting the fuzzed responses by size to get the user id's.
<img src="../assets/img/htb/startingpoint/Oopsie/big-bytes.png">


<img src="../assets/img/htb/startingpoint/Oopsie/John.png">
<img src="../assets/img/htb/startingpoint/Oopsie/Rafol.png">
<img src="../assets/img/htb/startingpoint/Oopsie/Peter.png">
<img src="../assets/img/htb/startingpoint/Oopsie/superadmin.png">


So from here I know from previous request editing that there is a cookie used by this site that takes this access ID as a parameter.  Let me pull it up in dev tools.
<img src="../assets/img/htb/startingpoint/Oopsie/cookies.png">


Now I'll try what I should have tried before fuzzing users.
<br/>
<img src="../assets/img/htb/startingpoint/Oopsie/superadmin-maybe
.png">


No dice.
<img src="../assets/img/htb/startingpoint/Oopsie/bad-cookie.png">


Trying the super admin access ID found from fuzzing.
<img src="../assets/img/htb/startingpoint/Oopsie/cookie1.png">


Looks like it worked!  A shell is close from this point.  Let's test the upload to see if it cares about extension.
<img src="../assets/img/htb/startingpoint/Oopsie/rms-uploads.png">


This is what we will try first. (I fixed the error in this after realizing my mistake.)
<img src="../assets/img/htb/startingpoint/Oopsie/test1.png">


Ruh Roh scoobs.
<img src="../assets/img/htb/startingpoint/Oopsie/ruhroe.png">
<img src="../assets/img/htb/startingpoint/Oopsie/phpinfo.png">


Grabbing pentestmonkey's quintessential php reverse shell and geting ready to catch the shell.
<img src="../assets/img/htb/startingpoint/Oopsie/preparing-shell.png">


Catch the shell.
<img src="../assets/img/htb/startingpoint/Oopsie/cath-shell.png">


Prop up a webserver to host linpeas and grab it from the reverse shell.
<img src="../assets/img/htb/startingpoint/Oopsie/linpeas.png">


I am officially tired of having a garbage reverse shell.  I don't remember where I pinched this from but this is a quick way to upgrade your shell.
<img src="../assets/img/htb/startingpoint/Oopsie/upgrade.png">


Run it.
<img src="../assets/img/htb/startingpoint/Oopsie/runit.png">


We already know this, but it's an interesting find by linpeas regardless.
<img src="../assets/img/htb/startingpoint/Oopsie/old-creds.png">


Heres the user flag.  I'm still thinking how to privesc to root.
<img src="../assets/img/htb/startingpoint/Oopsie/userflag.png">


This is a potential thing to look into.
<img src="../assets/img/htb/startingpoint/Oopsie/mysql.png">


And I think I know the robert -> root privesc path
<img src="../assets/img/htb/startingpoint/Oopsie/robert-id.png">
<img src="../assets/img/htb/startingpoint/Oopsie/linpeas-bug.png">


I am bashing my head against the wall on this one, but I will not check the walkthrough for at least another 45 minutes...
<img src="../assets/img/htb/startingpoint/Oopsie/frustrated.png">


So I was thinking about that MySQL database. Obviously I couldn't log in to it, but it has to be used for the website.  So I went to take a peek around the apache site and hit user-pwn gold.
<img src="../assets/img/htb/startingpoint/Oopsie/robert-pwn.png">
<img src="../assets/img/htb/startingpoint/Oopsie/robert-ssh.png">


Now to try and execute that root privesc that linpeas informed us about earlier.  There's a lot to see here and I explain it below.
<img src="../assets/img/htb/startingpoint/Oopsie/fin.png">


So basically linpeas told us that /usr/bin/bugtracker was calling cat with a relative path instead of an absolute one.  What this means is that we can write our own "cat" and essentially hijack the execution of that program.  This is usefull for privesc because the binary is owned by root and has the SUID bit set, meaning it executes as root when other users who are not root run it.  By adding our staging directory, where our custom cat command is, to the front of PATH, we essentially hijack all calls to cat that do not use a relative path.


I then proceed to troll myself by trying to cat the root flag.  It only works when I delete that malicious cat file.

## Thanks for reading!!!
