---
title: 'Split tunnelling with vpnc the easy way'
layout: post
---

I recently switched to running a clean Ubuntu Server with i3 as my window manager – this meant no more network connect, and thus I couldn’t simply use the checkbox “Use this connection only for resources on its network”.

So running a barebone vpnc, I finally figured out a simple way to split the traffic, so only traffic intended for the remote network runs through the VPN.

This configuration consists of 3 files, all of which I put in “/home/steffen/Scripts”:  
– vpn.sh  
– vpn.conf  
– vpnc-custom.sh

The two .sh files are executable shell scripts. (if that wasn’t obvious from the naming)

vpn.conf is your standard vpnc configuration file, mine looks like this:  
```
# vpn.conf
IPSec gateway URL_FOR_GATEWAY
IPSec ID IPSEC_ID
IPSec secret SECRET
Xauth username MY_USERNAME
Xauth password MY_PASSWORD
```

Note: URL\_FOR\_GATEWAY doesn’t have to be an URL – it can also just be an IP.

Right beside it I made this simple vpn.sh script (which is executable)

```
# vpn.sh
#!/bin/sh
sudo vpnc vpn.conf --script "/home/steffen/Scripts/vpnc-custom.sh"
sudo route del -net default dev tun0
sudo route add -net 192.168.1.0 netmask 255.255.255.0 dev tun0
sudo route add default gw 10.0.0.1
```

This establishes the VPN tunnel, then immediately removes the default route vpnc creates, which would route all traffic through the tunnel. It then adds a custom route for the subnet I need to reach through the tunnel instead. This way only traffic for 192.168.1.X is routed through the tunnel – all other traffic uses my normal default route (i.e. your normal LAN or Internet connection)  
And finally I had to add the original default route back in – do note that this may wary from distribution to distribution. I didn’t need this step on Linux Mint for instance, but I do on Ubuntu Server.

Finally there’s the vpnc-custom.sh script, which is a custom instruction for vpnc:  
```
# vpnc-custom.sh
#!/bin/sh
INTERNAL_IP4_DNS=
/usr/share/vpnc-scripts/vpnc-script
```

This simply sets the variable INTERNAL\_IP4\_DNS to nothing, which prevents vpnc from altering your resolv.conf. This is necessary as otherwise all DNS lookups would go through the tunnel, as vpnc automatically gets a new DNS server from the VPN connection.

Lastly it runs the standard vpnc-script, without which the tunnel would not work. Do note that the location of this script varies with distributions – on Ubuntu it’s located as in the code above. If you can’t locate it, you could either do a `find / -name vpnc-script` or check the man page for vpnc: `man vpnc`

This ALSO means you need to know the IP of whatever you try to reach through the VPN, as you no longer have access to the remote DNS server. I just put the few hostnames I need into /etc/hosts and called it a day 🙂

And that was all there was to it – I saw quite a few blog posts around the web with much more complicated setups, so while this may not be the “right” way to do it – it’s certainly easier AND works just as well.