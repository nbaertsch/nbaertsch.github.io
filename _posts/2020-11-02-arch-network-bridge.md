---
title: Arch Network Bridge
layout: post
category: post
---

So I have this nice 4-port nic that I bought with the same expectations as one would buy a raspberry pi.  I figured I would buy it, it would sit in the box for a few months, and then I would feel pressured into some project so I can justify the purchase.  This is that project.

# General Overview
So there are multiple ways to go about this. We could go the ARP poisoning route, or we could go the bridge route.  Obviously due to the aforementioned vacant ethernet ports, I am going to use a virtual bridge.  Another reason is that I want as little latency as possible.  I definitely didn't look this up but I assume ARP poisoning is going to have a higher cost than a virtual bridge.
<br/>

## Options for you kids with one port
Buy a cheap switch with port mirroring.  Look, [there's one](https://www.amazon.com/TP-LINK-TL-SG105E-5-Port-Gigabit-Version/dp/B00N0OHEMA/ref=sr_1_3?crid=1REON08RXKD7N&dchild=1&keywords=port%2Bmirroring%2Bswitch&qid=1604221251&s=electronics&sprefix=port%2Bmi%2Caps%2C148&sr=1-3&th=1)!
<br/>

Or you can do ARP poisoning.  Theres a link at the end for some further reading on that, but I won't discuss it here.  (It's on the list)
<br/>

# Actually Doing The Thing
Extremely easy to set up the bridge with iproute2 package:
```
ip link add name bridge_name type bridge
ip link set bridge_name up
```
<br/>

Just go read the [wiki](https://wiki.archlinux.org/index.php/Network_bridge#With_iproute2), it's not that scary.  We need to add both interfaces to the bridge, and bring the bridge up.  Make sure the interfaces are up to while your at it.  
<br/>

Enable IPv4 forwarding: `sysctl -w net.ipv4.ip_forward=1`. From the man page 
> sysctl  is  used  to  modify  kernel  parameters at runtime.  The parameters available are those listed under /proc/sys/

To make the change permanent you can add `net.ipv4.ip_forward=1` to a sysctl conf file.
<br/>

# Further Reading
- [Learn what a bridge is](https://geek-university.com/ccna/what-is-a-network-bridge/#:~:text=A%20network%20bridge%20is%20a,also%20improves%20the%20network%20performance)
- [Using ARP Cache Poisoning for Packet Analysis](https://chrissanders.org/2008/04/using-arp-cache-poisoning-for-packet-analysis/)
- [That cheap smart switch I mentioned](https://www.amazon.com/TP-LINK-TL-SG105E-5-Port-Gigabit-Version/dp/B00N0OHEMA/ref=sr_1_3?crid=1REON08RXKD7N&dchild=1&keywords=port%2Bmirroring%2Bswitch&qid=1604221251&s=electronics&sprefix=port%2Bmi%2Caps%2C148&sr=1-3&th=1)
- [Arch Wiki - Tap Networking With QEMU](https://wiki.archlinux.org/index.php/QEMU#Tap_networking_with_QEMU)
- [Arch Wiki - Network Bridge](https://wiki.archlinux.org/index.php/Network_bridge) (scroll to "Creating bridge manually")