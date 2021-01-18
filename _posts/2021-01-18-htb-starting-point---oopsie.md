---
title: HTB Starting Point - Oopsie
layout: post
category: writeup
---

# Oopsie
This box was a lot of fun to do because of how easy it was, and linux machines are just easier to wrap my brain in general.  There is a real sense of accomplishment from rooting a box without being hand-held through it.  I will concede that I looked at the walkthrough once because I didn't think to reuse passwords from the first box.  I also really enjoyed the web-app part of the challenge despite fumbling around the login page for way to long.  Enjoy this thorough walkthrough!


Initial recon.
<br/>
<img src="../assets/img/htb/startingpoint/Oopsie/nmap.png">
<br/>


Gobusted.  Everything is forbidden...
<br/>
<img src="../assets/img/htb/startingpoint/Oopsie/gobuster1.png">
<br/>


Zap spider didn't come up with much other than /cdn-cgi/login/script.js, but as you can see below it is an empty file.
<br/>
<img src="../assets/img/htb/startingpoint/Oopsie/zap1.png">
<br/>


Manually trying things that exepctedly don't work.
<br/>
<img src="../assets/img/htb/startingpoint/Oopsie/login1.png">
<br/>


This is an interesting find which is a great example of why you need to understand what your tools do and mannually check things.  Burp and Zap spidering only follow links.  Obviously the empty script.js file in this directory was called in the index as a clue as to where the login page would be, but there are no direct links to this page anywhere on the main page.
<br/>
<img src="../assets/img/htb/startingpoint/Oopsie/login2.png">
<br/>


I really like Zap because of the HUD integration in the browser. It allows you to intercept and modify requests without leaving the browser.
<br/>
<img src="../assets/img/htb/startingpoint/Oopsie/zaphud.png">
<br/>

This will most likely not work...
<br/>
<img src="../assets/img/htb/startingpoint/Oopsie/badsqli.png">
<br/>


At this point I broke down and looked at the walkthrough just for this step and.... it's a reused cred from Archetype.  I guess thats a thing we do here.  Megacorp refferences on this site I guess so I should have made the connection.
<br/>
<img src="../assets/img/htb/startingpoint/Oopsie/rms.png">
<br/>


Just poking around.  This looks like I can enumerate all user accounts.
<br/>
<img src="../assets/img/htb/startingpoint/Oopsie/potentialsqli.png">
<br/>


This is also interesting. Super admin rights?
<br/>
<img src="../assets/img/htb/startingpoint/Oopsie/super-admin-req.png">
<br/>


Preparing to fuzz user accounts. I tried manual incrementing and realized id's are not sequential. Or maybe there are no other users? Doubtfull.  I want to find this supposed super admin.
<br/>
<img src="../assets/img/htb/startingpoint/Oopsie/preparing-to-fuzz.png">
<br/>


Careless fuzzing.  This could take way to long...
<br/>
<img src="../assets/img/htb/startingpoint/Oopsie/careless-fuzzing.png">
<br/>


<br/>
I stopped fuzzing at around 4%...
<br/>


Sorting the fuzzed responses by size to get the user id's.
<br/>
<img src="../assets/img/htb/startingpoint/Oopsie/big-bytes.png">
<br/>

<br/>
<img src="../assets/img/htb/startingpoint/Oopsie/John.png">
<img src="../assets/img/htb/startingpoint/Oopsie/Rafol.png">
<img src="../assets/img/htb/startingpoint/Oopsie/Peter.png">
<img src="../assets/img/htb/startingpoint/Oopsie/superadmin.png">
<br/>


So from here I know from previous request editing that there is a cookie used by this site that takes this access ID as a parameter.  Let me pull it up in dev tools.
<br/>
<img src="../assets/img/htb/startingpoint/Oopsie/cookies.png">
<br/>


Now I'll try what I should have tried before fuzzing users.
<br/>
<img src="../assets/img/htb/startingpoint/Oopsie/superadmin-maybe.png">
<br/>


No dice.
<br/>
<img src="../assets/img/htb/startingpoint/Oopsie/bad-cookie.png">
<br/>


Trying the super admin access ID found from fuzzing.
<br/>
<img src="../assets/img/htb/startingpoint/Oopsie/cookie1.png">
<br/>


Looks like it worked!  A shell is close from this point.  Let's test the upload to see if it cares about extension.
<br/>
<img src="../assets/img/htb/startingpoint/Oopsie/rms-uploads.png">
<br/>

This is what we will try first. (I fixed the error in this after realizing my mistake.)
<br/>
<img src="../assets/img/htb/startingpoint/Oopsie/test1.png">
<br/>


Ruh Roh scoobs.
<br/>
<img src="../assets/img/htb/startingpoint/Oopsie/ruhroh.png">
<img src="../assets/img/htb/startingpoint/Oopsie/phpinfo.png">
<br/>


Grabbing pentestmonkey's quintessential php reverse shell and geting ready to catch the shell.
<br/>
<img src="../assets/img/htb/startingpoint/Oopsie/preparing-shell.png">
<br/>

Catch the shell.
<br/>
<img src="../assets/img/htb/startingpoint/Oopsie/catch-shell.png">
<br/>

Prop up a webserver to host linpeas and grab it from the reverse shell.
<br/>
<img src="../assets/img/htb/startingpoint/Oopsie/linpeas.png">
<br/>

I am officially tired of having a garbage reverse shell.  I don't remember where I pinched this from but this is a quick way to upgrade your shell.
<br/>
<img src="../assets/img/htb/startingpoint/Oopsie/upgrade.png">
<br/>


Run it.
<br/>
<img src="../assets/img/htb/startingpoint/Oopsie/runit.png">
<br/>

We already know this, but it's an interesting find by linpeas regardless.
<br/>
<img src="../assets/img/htb/startingpoint/Oopsie/old-creds.png">
<br/>


Heres the user flag.  I'm still thinking how to privesc to root.
<br/>
<img src="../assets/img/htb/startingpoint/Oopsie/userflag.png">
<br/>

This is a potential thing to look into.
<br/>
<img src="../assets/img/htb/startingpoint/Oopsie/mysql.png">
<br/>

And I think I know the robert -> root privesc path
<br/>
<img src="../assets/img/htb/startingpoint/Oopsie/robert-id.png">
<img src="../assets/img/htb/startingpoint/Oopsie/linpeas-bug.png">
<br/>

I am bashing my head against the wall on this one, but I will not check the walkthrough for at least another 45 minutes...
<br/>
<img src="../assets/img/htb/startingpoint/Oopsie/frustrated.png">
<br/>

So I was thinking about that MySQL database. Obviously I couldn't log in to it, but it has to be used for the website.  So I went to take a peek around the apache site and hit user-pwn gold.
<br/>
<img src="../assets/img/htb/startingpoint/Oopsie/robert-pwn.png">
<img src="../assets/img/htb/startingpoint/Oopsie/robert-ssh.png">
<br/>

Now to try and execute that root privesc that linpeas informed us about earlier.  There's a lot to see here and I explain it below.
<br/>
<img src="../assets/img/htb/startingpoint/Oopsie/fin.png">
<br/>


So basically linpeas told us that /usr/bin/bugtracker was calling cat with a relative path instead of an absolute one.  What this means is that we can write our own "cat" and essentially hijack the execution of that program.  This is usefull for privesc because the binary is owned by root and has the SUID bit set, meaning it executes as root when other users who are not root run it.  By adding our staging directory, where our custom cat command is, to the front of PATH, we essentially hijack all calls to cat that do not use a relative path.
<br/>


I then proceed to troll myself by trying to cat the root flag.  It only works when I delete that malicious cat file.

## Thanks for reading!!!
