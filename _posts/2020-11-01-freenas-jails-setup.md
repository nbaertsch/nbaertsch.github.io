---
title: FreeNAS-Jails-Setup
layout: post
category: post
---

My latest project was transforming an old ass AMD Phenom workstation into a home NAS.  FreeNAS is stupid simple to get up and running (if you do the prerequisite reading on ZFS) so this post is not about how I got that set up.  Instead, this post is a general document of the steps I took after the initial NAS setup to get some extra functionality out of the machine in the form of jails (BSD-flavored containerization).  Again, no one reads this so don't expect a guide, this is just incase the box explodes and I have to set everything up again!

# Jail IPs
The FreeNAS box itself is at **10.0.0.52**
1. deluge	@ **10.0.0.228:8112**
1. plex		@ **10.0.0.231:32400**
1. radarr	@ **10.0.0.52:7878 (NAT)**
1. sonarr	@ **10.0.0.52:8989 (NAT)**
1. jackett	@ **10.0.0.98:9117**

DHCP server will nuke these.

# jackett
The jackett plugin fails for one reason or another, so it's a standard jail now.
```sh
pkg update
pkg upgrade
pkg install jackett
sysrc "jackett_enable=YES"
service jackett start
```

# deluge
Deluge was having DNS problems as a plugin.  Either way, I rebuilt it by hand.
```sh
pkg update
pkg upgrade
pkg install deluge vim
sysrc "deluged_enable=YES" "deluged_user=nobody" "deluged_confdir=/usr/local/etc/deluge" "deluge_web_enable=YES" "deluge_web_user=nobody" "deluge_web_confdir=/usr/local/etc/deluge"
mkdir /usr/local/etc/deluge
chown nobody:nobody /usr/local/etc/deluge
```
<br/>
Note on ``nobody``:
> nobody is the generic unprivileged system account. However, the more services that use nobody, the more files and processes that user will become associated with, and hence the more privileged that user becomes.

<br/>

``pkg info deluge`` reveals that we are now running 2.0.3, compared to to the plugion which was severely unmaintaned at 1.5 or so if memory serves.  Hopefully this resolves the networking issues.

# OpenVPN setup
Setting up openVPN inside a jail is as simple as it would be on a linux box..
```sh
jls 					# list jails
jexec $jailnumber tcsh 	# open a shell in the jail
pkg update
pkg upgrade
pkg install openvpn wget
mkdir /usr/local/etc/openvpn 
cd !$
vim ./auth.txt			# username on line 1 pw on line 2
chmod 600 !$
mkdir ./download		# we will pull pia's ovpn list here and unzip it
cd !$
wget https://www.privateinternetaccess.com/openvpn/openvpn.zip --no-check-certificate
unzip openvpn.zip
cp $chosen-server-ovpn-conf ../openvpn.conf
cd ..
vim openvpn.conf 		# add absolute path to auth.txt as value after auth-user-pass
sysrc "openvpn_enable=YES" "openvpn_if=tun"		# must set allow_tun for this jail for this to work.
```
<br/>
Use ``wget -qO - http://wtfismyip.com/text`` to check external IP.  

Shutdown jail, mount datasets, restart and connect to the web interface.  

Start and stop openvpn with ``service openvpn start/stop/status`` respectively. <br/>

