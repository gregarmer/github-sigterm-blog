+++
date        = "2010-12-31T12:00:00-04:00"
title       = "Using ferm to build firewall rulesets"
description = ""
tags        = [ "unix", "security" ]
topics      = [ "unix" ]
slug        = "using-ferm-to-build-firewall-rulesets"
+++

This post is thanks to a suggestion from <a href="http://twitter.com/froztbyte">JP Viljoen</a> to check out <a href="http://ferm.foo-projects.org/">ferm</a>. Well, I did, and it's fairly neat. You get to express your firewall configuration in structures resembling simple C code along with using things like arrays, functions and if / else constructs which makes building complex rulesets quite a simple task.

I've included an example configuration below of one of my machines. The network configuration is not extremely complex, but there is a mix of IPv4, IPv6 and - as this is an IRC server - some DNAT to make the IRC service available on a number of other privileged ports without having the service actually listen on those ports. This particular server is running Debian however ferm is basically just a front to ip(6)tables so it'll run pretty much anywhere that runs.

<!--more-->

First off, here is my network interface configuration to give you an idea of what is where:

```console
kore:~# cat /etc/network/interfaces

auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
    address 173.134.21.19             # Static eth0 IP
    netmask 255.255.255.0
    gateway 173.134.21.1

iface eth0 inet6 static
    address 2001:410:1e9b:ba22::2     # Primary HE.net IPv6 /64 address
    netmask 64

auto eth0:0
iface eth0:0 inet static
    address 192.168.49.97             # Local networking
    netmask 255.255.128.0

auto he-ipv6
iface he-ipv6 inet6 v4tunnel
    address 2001:410:1e9a:ba22::2     # Tunnel address
    netmask 64
    ttl 255
    gateway 2001:410:1e9a:ba22::1
    endpoint 216.218.224.42
    local 173.134.21.19
```

There is nothing extremely complicated here, just a basic IPv4 static IP assigned by my provider, a local network for traffic between this and other local nodes, a <a href="http://tunnelbroker.net/">Hurricane Electric IPv6 tunnel</a> and a static IP from my HE.net provided /64.

The ferm configuration in use here looks like this:

```console
kore:~# cat /etc/ferm/ferm.conf
# -*- shell-script -*-
#
#  Configuration file for ferm(1).
#

@def $PORTS = (22 25 161 4949 6667 6668 7000 7352 7535); # Services running
@def $IRC_PORTS = (21 23 53 80 110 143 993);             # Additional ports

table filter {
    chain INPUT {
        policy DROP;

        # connection tracking
        mod state state INVALID DROP;
        mod state state (ESTABLISHED RELATED) ACCEPT;

        # allow local packages
        interface lo ACCEPT;

        # respond to ping
        proto icmp ACCEPT;

        # standard ports we allow from the outside
        proto tcp dport $PORTS ACCEPT;
    }

    chain OUTPUT {
        policy ACCEPT;

        # connection tracking
        #mod state state INVALID DROP;
        mod state state (ESTABLISHED RELATED) ACCEPT;
    }

    chain FORWARD {
        policy DROP;

        # connection tracking
        mod state state INVALID DROP;
        mod state state (ESTABLISHED RELATED) ACCEPT;
    }
}

table nat {
    chain PREROUTING {
        # additional ports we listen on and redirect to the IRC server
        interface eth0 proto tcp dport $IRC_PORTS DNAT to 173.134.21.19:6667;
    }
}

# IPv6:
domain ip6 table filter {
    chain INPUT {
        policy DROP;

        # connection tracking
        mod state state INVALID DROP;
        mod state state (ESTABLISHED RELATED) ACCEPT;

        # allow ICMP (for neighbor solicitation, like ARP for IPv4)
        proto ipv6-icmp ACCEPT;

        # standard ports we allow from the outside
        proto tcp dport $PORTS ACCEPT;
    }

    chain OUTPUT {
        policy ACCEPT;

        # connection tracking
        #mod state state INVALID DROP;
        mod state state (ESTABLISHED RELATED) ACCEPT;
    }

    chain FORWARD {
        policy DROP;

        # connection tracking
        mod state state INVALID DROP;
        mod state state (ESTABLISHED RELATED) ACCEPT;
    }
}
```

So this ruleset is basically broken down into 3 parts:

* IPv4 filter table
* IPv4 nat table
* IPv6 filter table

#### IPv4 filter table
We control the INPUT, OUTPUT and FORWARD chains here. On the INPUT chain, we default to dropping everything, enable connection state tracking, allow all traffic through our local interface, allow ICMP and specify a list of ports we allow the outside world to use. On the OUTPUT chain we allow everything out and enable connection state tracking. Finally on the FORWARD chain we drop everything as this machine is not a router. Pretty concise right ?

#### IPv4 nat table
In the nat table config, we basically setup the DNAT of those privileged ports under the PREROUTING chain.

#### IPv6 filter table
Finally, in the IPv6 filter table, we allow the same set of incoming ports as IPv4, allow ipv6-icmp and setup connection state tracking as before.

Once that's done, simply run:

```console
kore:~# ferm /etc/ferm/ferm.conf
```

 ... and your new ruleset is validated and loaded.


On a side note, if you are interested in playing around with IPv6 I would highly recommend setting up a Hurricane Electric tunnel and then doing the <a href="http://ipv6.he.net/certification/">certification</a>. It makes for a great Saturday afternoon time waster and you might learn something along the way:

<a href="http://ipv6.he.net/certification/"><img src="http://ipv6.he.net/certification/create_badge.php?pass_name=gregarmer&badge=1" alt="IPv6 Certification" width="150" /></a>
