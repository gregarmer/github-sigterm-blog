+++
date        = "2016-11-05T14:26:00-04:00"
title       = "Passing VLANs over an LACP trunk in OpenBSD"
description = ""
tags        = [ "openbsd", "networking" ]
topics      = [ "networking", "unix" ]
slug        = "passing-vlans-over-an-lacp-trunk-in-openbsd"
+++

It took me a little while to figure this out, so for anyone stuck on this, here's what I did.

<!--more-->

I did this with a Cisco 3750 switch.  The switch config was:

*Port-channel*

```
interface Port-channel42
 description etherchannel fw
 switchport trunk encapsulation dot1q
 switchport trunk allowed vlan 56-61,63
 switchport mode trunk
 spanning-tree portfast
 spanning-tree bpduguard enable
!
```

*Interfaces*

```
interface GigabitEthernet1/0/23
 description fw-pc42-1
 switchport trunk encapsulation dot1q
 switchport trunk allowed vlan 56-61,63
 switchport mode trunk
 channel-protocol lacp
 channel-group 42 mode active
!
interface GigabitEthernet1/0/24
 description fw-pc42-2
 switchport trunk encapsulation dot1q
 switchport trunk allowed vlan 56-61,63
 switchport mode trunk
 channel-protocol lacp
 channel-group 42 mode active
!
```

Then on the firewall:

```
# cat /etc/hostname.em0
up
# cat /etc/hostname.em1
up
# cat /etc/hostname.trunk0
trunkproto lacp trunkport em0 trunkport em1
up
```

and lastly the VLAN interfaces:

```
# cat /etc/hostname.vlan57
inet 172.29.57.1 255.255.255.0 NONE vlan 57 vlandev trunk0 descr WIFI
# cat /etc/hostname.vlan58
inet 172.29.58.1 255.255.254.0 NONE vlan 58 vlandev trunk0 descr SERVERS
# cat /etc/hostname.vlan60
inet 172.29.60.1 255.255.255.0 NONE vlan 60 vlandev trunk0 descr WIRED
# cat /etc/hostname.vlan61
inet 172.29.61.1 255.255.255.0 NONE vlan 61 vlandev trunk0 descr IPMI
# cat /etc/hostname.vlan63
inet 172.29.63.1 255.255.255.0 NONE vlan 63 vlandev trunk0 descr MGMT
```

You can set this up by executing `sh /etc/netstart` without having to run a bunch of `ifconfig` commands.

Your interfaces should look like this when it's up:

```
em0: flags=8b43<UP,BROADCAST,RUNNING,PROMISC,ALLMULTI,SIMPLEX,MULTICAST> mtu 1500
        lladdr 0c:c4:7a:ac:70:02
        index 1 priority 0 llprio 3
        trunk: trunkdev trunk0
        media: Ethernet autoselect (1000baseT full-duplex)
        status: active

em1: flags=8b43<UP,BROADCAST,RUNNING,PROMISC,ALLMULTI,SIMPLEX,MULTICAST> mtu 1500
        lladdr 0c:c4:7a:ac:70:02
        index 2 priority 0 llprio 3
        trunk: trunkdev trunk0
        media: Ethernet autoselect (1000baseT full-duplex)
        status: active

trunk0: flags=8843<UP,BROADCAST,RUNNING,SIMPLEX,MULTICAST> mtu 1500
        lladdr 0c:c4:7a:ac:70:02
        index 8 priority 0 llprio 3
        trunk: trunkproto lacp
        trunk id: [(8000,0c:c4:7a:ac:70:02,4044,0000,0000),
                 (8000,00:1a:e2:1f:0b:00,002A,0000,0000)]
                trunkport em1 active,collecting,distributing
                trunkport em0 active,collecting,distributing
        groups: trunk
        media: Ethernet autoselect
        status: active

vlan58: flags=8843<UP,BROADCAST,RUNNING,SIMPLEX,MULTICAST> mtu 1500
        lladdr 0c:c4:7a:ac:70:02
        description: SERVERS
        index 11 priority 0 llprio 3
        vlan: 58 parent interface: trunk0
        vnetid: 58
        parent: trunk0
        groups: vlan
        status: active
        inet 172.29.58.1 netmask 0xfffffe00 broadcast 172.29.59.255
```

and the switch:

```
core-3750-1#show etherchannel summary
Flags:  D - down        P - bundled in port-channel
        I - stand-alone s - suspended
        H - Hot-standby (LACP only)
        R - Layer3      S - Layer2
        U - in use      f - failed to allocate aggregator

        M - not in use, minimum links not met
        u - unsuitable for bundling
        w - waiting to be aggregated
        d - default port


Number of channel-groups in use: 3
Number of aggregators:           3

Group  Port-channel  Protocol    Ports
------+-------------+-----------+-----------------------------------------------
1      Po1(SU)         LACP      Gi1/0/15(P) Gi1/0/16(P)
2      Po2(SU)         LACP      Gi1/0/13(P) Gi1/0/14(P)
42     Po42(SU)        LACP      Gi1/0/23(P) Gi1/0/24(P)
```

A few things that got me:

* You will get an error if the `em*` interfaces are not marked as `up` when you add them as trunkports.  Set them as up with `ifconfig em0 up` before your `ifconfig trunk0 trunkport em0` command.
* When the tunnel is configured with `ifconfig` it doesn't automatically start, so you'll need an explicit `ifconfig trunk0 up` to bring it up.
