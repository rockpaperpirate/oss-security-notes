# `ip` commands

| `ip` command | Description |
| ---- | ---- |
| `ip a` | List all the network interfaces |
| `ip a s <dev>` | Short for `ip address show <device name>` |
|  |  |
## Linux IP addresses 

Use: `ip addr add <ip address>/<cidr> dev <device>`

Examples:

```
ip addr add 198.51.100.206/24 dev eth3
ip addr add 2001:db8:64ce:c633::2/64 dev eth3
```

And similarly for routes, use: 

`ip route add <network_ip>/<cidr> via <gateway_ip>`

Examples:

```
ip route add default via 198.51.100.1 dev eth3
ip route add default via 2001:db8:64c3:c633::1 dev eth3
```

### Modifying `/etc/network/interfaces`

Ref: https://www.cyberciti.biz/faq/setting-up-an-network-interfaces-file/

To setup `eth0` to dhcp settings, enter:

```
# /etc/network/interfaces
auto eth0
	iface eth0 inet dhcp
```

An example ethernet card setup: 
> **NOTE:**
> The broadcast and gateway fields are both optional.

```
# /etc/network/interfaces
auto eth0
	iface eth0 inet static
		address 192.168.0.42
		network 192.168.0.0
		netmask 255.255.255.0
		broadcast 192.168.0.255
		gateway 192.168.0.1
```

A more complicated ethernet setup, with a less common netmask, and a downright weird broadcast address:

```
# /etc/network/interfaces
auto eth0
	iface eth0 inet static
		address 192.168.1.42
		network 192.168.1.0
		netmask 255.255.255.128
		broadcast 192.168.1.0
		up route add -net 192.168.1.128 netmask 255.255.255.128 gw 192.168.1.2
		up route add default gw 192.168.1.200
		down route del default gw 192.168.1.200
		down route del -net 192.168.1.128 netmask 255.255.255.128 gw 192.168.1.2
```

> **NOTE:** 
> The `up` lines are executed verbatim when the interface is brought up, the `down` lines when it's brought down

## Static IP addresses on CentOS 7 or RHEL 7
### Modifying `/etc/syconfig/network-scripts`

> **NOTE:**
> This is generally for `CentOS 7 / RHEL 7`
> 

This assume a device named `eth0` is available on network resources. Start by opening it with elevated privileges: `vi /etc/sysconfig/network-scripts/ifcfg-eth0`

You can a DHCP or static configuration. Here is a typical DHCP configuration for `eth0`:

> **NOTE:**
> **CHECK THE DEVICE NAME!**
> The above device would typically be stored in ` /etc/sysconfig/network-scripts/ifcfg-eth0`

```
DEVICE="eth0"
ONBOOT=yes
NETBOOT=yes
UUID="41171a6f-bce1-44de-8a6e-cf5e782f8bd6"
IPV6INIT=yes
BOOTPROTO=dhcp
HWADDR="00:08:a2:0a:ba:b8"
TYPE=Ethernet
NAME="eth0"

```

Here is a typical static configuration:

```

HWADDR=00:08:A2:0A:BA:B8
TYPE=Ethernet
BOOTPROTO=none
# Server IP #
IPADDR=192.168.2.203
# Subnet #
PREFIX=24
# Set default gateway IP #
GATEWAY=192.168.2.254
# Set dns servers #
DNS1=192.168.2.254
DNS2=8.8.8.8
DNS3=8.8.4.4
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
# Disable ipv6 #
IPV6INIT=no
NAME=eth0
# This is system specific and can be created using 'uuidgen eth0' command #
UUID=41171a6f-bce1-44de-8a6e-cf5e782f8bd6
DEVICE=eth0
ONBOOT=yes
```

Be sure to restart the network service using `systemctl restart network`


