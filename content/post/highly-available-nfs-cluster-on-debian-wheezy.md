+++
date        = "2014-02-01T12:00:00-04:00"
title       = "Highly Available NFS Cluster on Debian Wheezy"
description = ""
tags        = [ "debian", "linux" ]
topics      = [ "linux" ]
slug        = "highly-available-nfs-cluster-on-debian-wheezy"
+++

#### Overview

This guide will help you setup a highly available NFS server on Debian Wheezy. This is a relatively battle-tested configuration, and there is plenty information out there on how it works - I'll include some links at the end of this post.

I was using GlusterFS up until recently, but I'm not happy with file corruption issues I'm seeing, and the insane load it puts on 2 rather beefy servers trying to resync data after one fails for just a short time.

<!--more-->

This guide will give you a setup as follows:

* One active NFS server with its own public, private and floating IP (VIP)
* One passive hot standby NFS server with its own public and private IP
* Automatic failover when one of the nodes becomes unresponsive or unreachable.
* Unicast cluster syncronization (so it works on Linode and other places where multicast (like corosync) isn't available.

### Servers

While writing this guide, I used 2 virtualbox VMs (alice and bob). Each VM configured as follows:

* Default Debian Wheezy install from a netinst iso
* 512MB RAM
* 1 x 8GB OS disk (all partitions - /dev/sda)
* 1 x 10GB data disk (/dev/sdb)
* Bridged networking (so it's easier to test)
    * alice has IP 192.168.1.201
    * bob has IP 192.168.1.202
    * Our floating virtual IP will be 192.168.1.200

### Configurations

It's good to get some basics down first.  

#### Packages

Let's start with some useful packages (install on alice and bob):

```
alice $ apt-get install ntp vim
```

#### Networking

Tweak to suit your environment, of course.

```
alice $ cat /etc/network/interfaces
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).

# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
auto eth0
iface eth0 inet static
    address 192.168.1.201
    netmask 255.255.255.0
    gateway 192.168.1.1
```

The config will be the same on bob, apart from the IP, which will be 192.168.1.202

#### Hostname

This is pretty important, since pretty much everything relies on the servers hostnames.

```
alice $ hostname alice.local
alice $ echo alice.local > /etc/hostname
bob $ hostname bob.local
bob $ echo bob.local > /etc/hostname
```

#### Install and Configure DRBD

DRBD will be used to constantly sync all data from the primary to the slave, whichever servers they may be at that point in time.

Install DRBD8 utils on alice and bob:

```
alice $ apt-get install drbd8-utils
```

Drop in the configs on alice and bob. First /etc/drbd.d/global_common.conf

```
global {
    usage-count yes;
}

common {
    protocol C;

    handlers {
        pri-on-incon-degr "/usr/lib/drbd/notify-pri-on-incon-degr.sh; /usr/lib/drbd/notify-emergency-reboot.sh; echo b > /proc/sysrq-trigger; reboot -f";
        pri-lost-after-sb "/usr/lib/drbd/notify-pri-lost-after-sb.sh; /usr/lib/drbd/notify-emergency-reboot.sh; echo b > /proc/sysrq-trigger; reboot -f";
        local-io-error "/usr/lib/drbd/notify-io-error.sh; /usr/lib/drbd/notify-emergency-shutdown.sh; echo o > /proc/sysrq-trigger; halt -f";

        split-brain "/usr/lib/drbd/notify-split-brain.sh root";
    }

    startup {
        wfc-timeout 15;
        degr-wfc-timeout 60;
    }

    net {
        cram-hmac-alg sha1;
    }

    syncer {
        rate 10M;
    }
}
```

and your DRBD resource config in /etc/drbd.d/r0.res

```
resource r0 {
    net {
        shared-secret "s3kr1t";
    }

    on alice {
        device    /dev/drbd0;
        disk      /dev/sdb;
        address   192.168.1.201:7788;
        meta-disk internal;
    }

    on bob {
        device    /dev/drbd0;
        disk      /dev/sdb;
        address   192.168.1.202:7788;
        meta-disk internal;
    }
}
```

Let's get DRBD started so the initial sync can get going.

```
# Create metadata on alice
alice $ drbdadm create-md r0

# Start DRBD on both nodes
alice $ /etc/init.d/drbd start
bob $ /etc/init.d/drbd start

# Setup primary DRBD connection and sync
alice $ drbdadm -- --overwrite-data-of-peer primary r0
alice $ drbdadm disconnect r0
alice $ drbdadm connect r0
```

You can check the sync status with this command:

```
alice $ cat /proc/drbd
version: 8.3.10 (api:88/proto:86-96)
built-in
 0: cs:SyncSource ro:Primary/Secondary ds:UpToDate/Inconsistent C r-----
    ns:3116484 nr:0 dw:0 dr:3593516 al:0 bm:219 lo:0 pe:2 ua:1 ap:0 ep:1 wo:f oos:1649980
    [============>.......] sync'ed: 65.5% (1608/4652) finish: 0:05:08 speed: 5,328 (5,056) K/sec
```

You can now format the DRBD disk using any filesystem you prefer, here I'm using EXT4

```
alice $ mkfs.ext4 /dev/drbd0
```

Add the DRBD disk to /etc/fstab on alice and bob

```
alice $ vi /etc/fstab

# Add a line like this - substitute for your preferred fs and settings
/dev/drbd0      /data           ext4    defaults        0 0
```

#### Install and Configure NFS

NFS will be used to serve our highly available data.

Let's start with installing some packages on alice and bob

```
alice $ apt-get install nfs-kernel-server
```

Now tell the new dependency based booting not to start NFS automatically.  NFS will be started automatically by heartbeat later on.

```
alice $ insserv --remove nfs-common
alice $ insserv --remove nfs-kernel-server
```

Setup our exports on alice and bob

```
alice $ vi /etc/exports

# Add a line similar to this, change to suit your network and requirements
/data   192.168.1.0/255.255.255.0(rw,no_root_squash,no_all_squash,sync)
```

#### Install and Configure heartbeat

We'll use heartbeat to syncronize the cluster, handle fencing and to promote the slave to the master.

Install heartbeat on alice and bob

```
alice $ apt-get install heartbeat
```

Drop in the configs on alice and bob as below.  You'll need 3 files in total.

/etc/heartbeat/ha.cf
```
logfacility     local0
keepalive 2
deadtime 10
bcast   eth0
auto_failback off
node alice bob
```

/etc/heartbeat/haresources
```
alice  IPaddr::192.168.1.200/24/eth0 drbddisk::r0 Filesystem::/dev/drbd0::/data::ext4 nfs-kernel-server
```

*Note:* this line starts with *alice* on both nodes - this is the "preferred primary".

/etc/heartbeat/authkeys
```
auth 3
3 md5 s3kr1t
```

*Note:* set a good password, especially if you deploy this in a public cloud!

Finally, start up heartbeat:
```
alice $ /etc/init.d/heartbeat start
```

If you run *ifconfig* on alice, you should see that it now has the floating IP. You'll also notice the NFS is running, and /data is mounted.

#### Testing

This is the best part.  Let's kill the primary server and make sure the slave takes over seamlessly.

The simplest way to simulate a failure is to stop heartbeat on whichever server is currently the primary.
```
alice $ /etc/init.d/heartbeat stop
```

Within a few seconds, you should see all services move over to bob, including the floating IP, NFS, and the /data mount.  A quick check of *cat /proc/drbd* should also show bob set to Primary.

### References

Finally, the links I promised in the beginning of this post.

* Why the servers are named [Alice and Bob](http://en.wikipedia.org/wiki/Alice_and_Bob)
* An [older guide](http://www.howtoforge.com/high_availability_nfs_drbd_heartbeat) I used while figuring this out
* [Linode HA info](http://version2beta.com/articles/high-availability-linode-pairs/)
* [Heartbeat](http://linux-ha.org/wiki/Heartbeat)
* [DRBD 8.3.x Users Guide](http://www.drbd.org/users-guide-8.3/)
