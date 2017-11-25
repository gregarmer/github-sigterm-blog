+++
date        = "2017-07-29T22:27:00-04:00"
title       = "Building a home lab"
description = ""
tags        = [ "unix", "linux", "networking" ]
topics      = [ "networking", "unix" ]
slug        = "building-a-home-lab"
thumbnail   = "/static/home-lab-servers.jpg"
+++

I've been setting up a small home lab for testing various things out and I
needed some space for virtual machines. I don't have any requirements for
insane performance, but I also didn't want a really sluggish setup, so here's
what I did...

<!--more-->

I have 3 nodes running ProxMox to act as hypervisors. A Synology NAS providing
shared storage. A Juniper EX2200-24 layer 3 switch handling the LACP / VLANs
and a Juniper SRX340 as the firewall / router. Racked in [Navepoint 18U] fan
cooled rack and powered by two [CyberPower 1.5kVa UPS] units.

[Navepoint 18U]: https://www.amazon.com/gp/product/B00P4SVZ4S/
[CyberPower 1.5kVa UPS]: https://www.amazon.com/gp/product/B0016P7HJA/

##### Hypervisors

The hypervisors are [SuperMicro 1U SYS-5018A-TN4] boxes each with 8 core Intel
Atom C2750 CPUs running @ 2.40GHz, along with [32GB DDR3-1600 SO-DIMM ECC
memory] and a [256GB SSD] drive for local storage.

![](/static/proxmox.png)

[SuperMicro 1U SYS-5018A-TN4]: https://www.amazon.com/gp/product/B00FN1OQVA/
[32GB DDR3-1600 SO-DIMM ECC memory]: https://www.amazon.com/gp/product/B00CUYOGRM/
[256GB SSD]: https://www.amazon.com/gp/product/B00LMXBOP4/

##### Storage

For storage I have the [Synology RS-815+] with the [RX-415 4 bay expansion]
unit. Six of the bays are filled with a [3TB WD RED] disk and the final two
bays each have a [512GB SSD] disk which makes up the read/write cache.

All 6 disks are part of a single disk group, which is then split into 2 disk
volumes - 1 backed by the SSD cache, and 1 without SSD cache which is used for
backups and infrequently accessed data.  They look like this:

![](/static/synology-storage.png)

The SSD cache is then configured in mirrored RAID 1:

![](/static/synology-cache.png)

[Synology RS-815+]: https://www.amazon.com/gp/product/B00ST05IF0/
[RX-415 4 bay expansion]: https://www.amazon.com/gp/product/B00NNQFC56/
[3TB WD RED]: https://www.amazon.com/gp/product/B008JJLW4M/
[512GB SSD]: https://www.amazon.com/gp/product/B00LF10KTO/

##### Network

The network is handled by a Juniper EX2200 switch with a trunk out to a Juniper
SRX340 firewall and router.  The switch handles a few VLANs for various parts
of the network, and does LACP for the storage and firewall trunk.

There are a couple VLAN access ports configured:

* SERVERS - public side VM traffic
* PRIVATE - private side VM traffic (ProxMox cluster multicast data)
* STORAGE - NFS / iSCSI LUN traffic
* MANAGEMENT - IPMI and other management traffic
* WIRED - My LAN at home
* WIFI - My WIFI network

The SERVERS, MANAGEMENT, WIRED and WIFI vlan's are members in the firewall
trunk.  All other traffic doesn't leave the switch.

For storage, each hypervisor and the Synology NAS have 2 x 1GbE NIC ports
configured as part of an LACP bond with jumbo frames:

*Switch side*

```console
ae2 {
    description pve-1;
    traceoptions {
        flag all;
    }
    mtu 9200;
    aggregated-ether-options {
        lacp {
            active;
        }
    }
    unit 0 {
        family ethernet-switching {
            port-mode access;
            vlan {
                members STORAGE;
            }
        }
    }
}
```

*Hypervisor side*

```console
root@pve-1:~# grep -A5 'iface bond0' /etc/network/interfaces
iface bond0 inet manual
        slaves eth2 eth3
        bond_miimon 100
        bond_mode 802.3ad
        bond_xmit_hash_policy layer3+4
        post-up ifconfig bond0 mtu 9000
```

##### Physical Connectivity

Each hypervisor has 5 network ports - 1 for IPMI and 4 x 1GbE NIC's.  IPMI is
plugged into the switch on a MANAGEMENT VLAN access port.  Two ports are part
of the LACP bond for storage, and the final two are for public and private
traffic, each connected to an access port for the SERVERS and PRIVATE VLAN
respectively.

The switch then has 2 ports in another LACP bond for the trunk out to the
firewall.  The VLANs that are part of that trunk are then split up into firewall
zones where specific policies are applied.

##### Noise Level, Performance, Power Consumption, etc

I'll be writing a series of upcoming posts on how I handled these things along
with some metrics around the performance and impact to my electric bill. Stay
tuned...
