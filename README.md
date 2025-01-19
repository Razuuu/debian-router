# Use a Debian server as a Router

This is my configuration to have a Debian server as a router, this can be edited for its own.

# Specs
**Zyxel VMG3006-D70A**  

![Zyxel VMG3006-D70A](https://cdn.razuuu.de/gh/debian-router/img_vmg3006-d70a_p500px_transparent.png)  
`LAN1`: connected to `eth2`  
`LAN2`: connected to `eth1`  
`DSL`: `RJ11 / DSL Cable connected`  

##
**Zyxel Gigabit Ethernet Switch GS-108B v3**  

![Zyxel Gigabit Ethernet Switch GS-108B v3](https://cdn.razuuu.de/gh/debian-router/img_gs-108b-v3_p_600transparent.png)  
`LAN8`: connected to `eth1`  
All other ports for clients.  

##

**Debian server (of course)**

`eth0`: PPPoE connection  
`eth1`: DHCP Server  
`eth2`: Modem's webinterface  

# PPPoE Setup
Use `pppoeconf` to configure the PPPoE connection.  
Install via apt or copy the file from a USB stick to the server.  

# Setup
Keep the system up-to-date and install all necessary packages below.

**Add stork Debian repository**  
```
apt update
apt install curl
curl -1sLf 'https://dl.cloudsmith.io/public/isc/stork/cfg/setup/bash.deb.sh' | sudo bash
```

**After that install this**  

```
apt install firmware-realtek resolvconf ntp dnsutils net-tools bridge-utils bind9 nftables nano kea-{dhcp4,dhcp6}-server isc-stork-server vnstat
```

# Network

**Enable IPv4 forward for NAT purposes**  
```
echo "net.ipv4.ip_forward=1" > /etc/sysctl.d/ip-forward.conf
sysctl -p
```

**Use our nameservers**  
```
echo -e "# Bind9 nameserver\nnameserver 127.0.0.1\nnameserver ::1" > /etc/resolv.conf
```

**Nftables WIP**  



