---
title: HTB Starting Point - Archetype
layout: post
category: writeup
---

# Archetype
Because I am not at all versed in attacking windows machines, I will be following the HTB walkthrough almost to a T.


Recon the machine.  Notice RPC and ms-sql.
<br/>
<img src="../assets/img/htb/startingpoint/Archetype/nmap.png">
<br/>


Use smbclient to enumerate network shares.
<br/>
<img src="../assets/img/htb/startingpoint/Archetype/smb1.png">
<br/>


Try and connect to the backups share anonymously.
<br/>
<img src="../assets/img/htb/startingpoint/Archetype/smb-help.png">
<br/>


Connected successfully without a password.  Here is the help for smbclient.
<br/>
<img src="../assets/img/htb/startingpoint/Archetype/smb2.png">
<br/>


Grab the only file in the share.  .dtsconfig files are used to apply propertiy values to services that integrate with sql servers.  We can see the username and password used to authenticate to the sql server.
<br/>
<img src="../assets/img/htb/startingpoint/Archetype/dtsconfig.png">
<br/>


We will use mssqlclioent.py from Impacket to authenticate to the server.
<br/>
<img src="../assets/img/htb/startingpoint/Archetype/mssqlclient.png">
<br/>


Here are the sql server credentials again, in an easier to work with format.
<br/>
<img src="../assets/img/htb/startingpoint/Archetype/sqlcreds.png">
<br/>


Authenticate to the server and get a SQL prompt.
<br/>
<img src="../assets/img/htb/startingpoint/Archetype/mssqlclient2.png">
<br/>


Here are mssqlclient.py specific commands. These help automate command execution.
<br/>
<img src="../assets/img/htb/startingpoint/Archetype/mssqlclienthelp.png">
<br/>


Opening firewall for reverse shell.
<br/>
<img src="../assets/img/htb/startingpoint/Archetype/ufwallow.png">
<br/>


Opened two new tmux panes; The top is for a webserver to host the reverse shell download and the bottom is the netcat listener to catch the shell.
<br/>
<img src="../assets/img/htb/startingpoint/Archetype/revshell-before.png">
<br/>


Used the sql server rce to download the reverse shell and received the connection in the nc listener.
<br/>
<img src="../assets/img/htb/startingpoint/Archetype/revshell-after.png">
<br/>


The flag of the sql_svc user.
<br/>
<img src="../assets/img/htb/startingpoint/Archetype/userflag.png">
<br/>


Look at the powershell history for that user.  We see admin creds.
<br/>
<img src="../assets/img/htb/startingpoint/Archetype/privesc.png">
<br/>


Use psexec.py from Impacket to login to an interactive powershell console as administrator.
<br/>
<img src="../assets/img/htb/startingpoint/Archetype/psexec.png">
<br/>


The root flag.
<br/>
<img src="../assets/img/htb/startingpoint/Archetype/rootflag.png">
<br/>
