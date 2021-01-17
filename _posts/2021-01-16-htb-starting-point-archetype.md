---
title: HTB Starting Point - Archetype
layout: post
category: writeup
---

# Archetype

Recon the machine.  Notice RPC and ms-sql.
<img src="../assets/img/htb/startingpoint/Archetype/nmap.png">


Use smbclient to enumerate network shares.
<img src="../assets/img/htb/startingpoint/Archetype/smb1.png">


Try and connect to the backups share anonymously.
<img src="../assets/img/htb/startingpoint/Archetype/smb-help.png">


Connected successfully without a password.  Here is the help for smbclient.
<img src="../assets/img/htb/startingpoint/Archetype/smb2.png">


Grab the only file in the share.  .dtsconfig files are used to apply propertiy values to services that integrate with sql servers.  We can see the username and password used to authenticate to the sql server.
<img src="../assets/img/htb/startingpoint/Archetype/dtsconfig.png">


We will use mssqlclioent.py from Impacket to authenticate to the server.
<img src="../assets/img/htb/startingpoint/Archetype/mssqlclient.png">


Here are the sql server credentials again, in an easier to work with format.
<img src="../assets/img/htb/startingpoint/Archetype/sqlcreds.png">


Authenticate to the server and get a SQL prompt.
<img src="../assets/img/htb/startingpoint/Archetype/mssqlclient2.png">


Here are mssqlclient.py specific commands. These help automate command execution.
<img src="../assets/img/htb/startingpoint/Archetype/mssqlclienthelp.png">


Opening firewall for reverse shell.
<img src="../assets/img/htb/startingpoint/Archetype/ufwallow.png">


Opened two new tmux panes; The top is for a webserver to host the reverse shell download and the bottom is the netcat listener to catch the shell.
<img src="../assets/img/htb/startingpoint/Archetype/revshell-before.png">


Used the sql server rce to download the reverse shell and received the connection in the nc listener.
<img src="../assets/img/htb/startingpoint/Archetype/revshell-after.png">


The flag of the sql_svc user.
<img src="../assets/img/htb/startingpoint/Archetype/userflag.png">


Look at the powershell history for that user.  We see admin creds.
<img src="../assets/img/htb/startingpoint/Archetype/privesc.png">


Use psexec.py from Impacket to login to an interactive powershell console as administrator.
<img src="../assets/img/htb/startingpoint/Archetype/psexec.png">


The root flag.
<img src="../assets/img/htb/startingpoint/Archetype/rootflag.png">