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
echo "#net.ipv6.conf.all.forwarding=1" >> /etc/sysctl.d/ip-forward.conf
sysctl -p
```

**Use our nameservers**  
```
echo -e "# Bind9 nameserver\nnameserver 127.0.0.1\nnameserver ::1" > /etc/resolv.conf
```

**Nftables WIP**  

Use UFW instead, easier and uses nftables as base

All open ports for outside.  
Allow every port for the 10.3.10.0/24 subnet as well  
DHCPv6 replies needed when using IPv6  

```
$ ufw status
Status: active

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW       Anywhere                   # SSH TCP
25/tcp                     ALLOW       Anywhere                   # SMTP TCP
53/tcp                     ALLOW       Anywhere                   # Bind9 TCP
80/tcp                     ALLOW       Anywhere                   # HTTP TCP
143/tcp                    ALLOW       Anywhere                   # IMAP TCP
443                        ALLOW       Anywhere                   # HTTP/s TCP
465/tcp                    ALLOW       Anywhere                   # SMTP SSL/TLS TCP
587/tcp                    ALLOW       Anywhere                   # SMTP MSA TCP
993/tcp                    ALLOW       Anywhere                   # IMAP Encrypted TCP
7777/tcp                   ALLOW       Anywhere                   # SA:MP TCP
25565/tcp                  ALLOW       Anywhere                   # Minecraft TCP
30033/tcp                  ALLOW       Anywhere                   # TeamSpeak 3 Filetransfer TCP
30814/tcp                  ALLOW       Anywhere                   # BeamMP TCP
53/udp                     ALLOW       Anywhere                   # Bind9 UDP
80/udp                     ALLOW       Anywhere                   # HTTP UDP
443/udp                    ALLOW       Anywhere                   # HTTP/s UDP
546/udp                    ALLOW       Anywhere                   # Allow DHCPv6 replies see https://github.com/openwrt/openwrt/issues/5066 UDP
9987/udp                   ALLOW       Anywhere                   # TeamSpeak Voice UDP
7777/udp                   ALLOW       Anywhere                   # SA:MP UDP
30814/udp                  ALLOW       Anywhere                   # BeamMP UDP
51820/udp                  ALLOW       Anywhere                   # WireGuard UDP
Anywhere                   ALLOW       10.3.10.0/24
22/tcp (v6)                ALLOW       Anywhere (v6)              # SSH TCP
25/tcp (v6)                ALLOW       Anywhere (v6)              # SMTP TCP
53/tcp (v6)                ALLOW       Anywhere (v6)              # Bind9 TCP
80/tcp (v6)                ALLOW       Anywhere (v6)              # HTTP TCP
143/tcp (v6)               ALLOW       Anywhere (v6)              # IMAP TCP
443 (v6)                   ALLOW       Anywhere (v6)              # HTTP/s TCP
465/tcp (v6)               ALLOW       Anywhere (v6)              # SMTP SSL/TLS TCP
587/tcp (v6)               ALLOW       Anywhere (v6)              # SMTP MSA TCP
993/tcp (v6)               ALLOW       Anywhere (v6)              # IMAP Encrypted TCP
7777/tcp (v6)              ALLOW       Anywhere (v6)              # SA:MP TCP
25565/tcp (v6)             ALLOW       Anywhere (v6)              # Minecraft TCP
30033/tcp (v6)             ALLOW       Anywhere (v6)              # TeamSpeak 3 Filetransfer TCP
30814/tcp (v6)             ALLOW       Anywhere (v6)              # BeamMP TCP
53/udp (v6)                ALLOW       Anywhere (v6)              # Bind9 UDP
80/udp (v6)                ALLOW       Anywhere (v6)              # HTTP UDP
443/udp (v6)               ALLOW       Anywhere (v6)              # HTTP/s UDP
546/udp (v6)               ALLOW       Anywhere (v6)              # Allow DHCPv6 replies see https://github.com/openwrt/openwrt/issues/5066 UDP
9987/udp (v6)              ALLOW       Anywhere (v6)              # TeamSpeak Voice UDP
7777/udp (v6)              ALLOW       Anywhere (v6)              # SA:MP UDP
30814/udp (v6)             ALLOW       Anywhere (v6)              # BeamMP UDP
51820/udp (v6)             ALLOW       Anywhere (v6)              # WireGuard UDP
```
