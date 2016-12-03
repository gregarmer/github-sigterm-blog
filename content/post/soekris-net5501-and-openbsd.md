+++
date = "2016-12-03T12:05:38-05:00"
title = "Soekris net5501 and OpenBSD"
banner = ""
images = [
]
tags = [
	"unix",
	"openbsd",
]
categories = [
]
description = ""
menu = ""
draft = true

+++

This post has been sitting in my drafts for a while now, but I recently upgraded my little [Soekris firewall](http://soekris.com/products/net5501.html) and thought I'd use the opportunity to finish it.

<!--more-->

So I have the [Soekris net5501-70](http://soekris.com/products/net5501.html) running OpenBSD on [8GB Compact Flash](http://soekris.com/products/accessories/sandisk-8gb.html).

The inside of the case looks like [this](https://sigterm.sh/wp-content/uploads/2012/04/soekris-net5501-1024x685.jpg).

I also have a 120GB 2.5" HDD which I use for write-heavy things like syslog, traffic accounting and Squid caching. The core OS filesystems on the CF card are mounted in readonly mode to prevent corruption during unexpected reboots as well as a lot of [wear leveling](http://en.wikipedia.org/wiki/Wear_leveling).

So let's get to it, here is how to connect and install OpenBSD onto Compact Flash for a Soekris net5501. First you'll need a couple of things:

* A Soekris net5501 (although this "how-to" should work equally as well on the smaller models).
* A 9 pin null-modem cable - You can [pick one up](http://soekris.com/products/accessories/null-modem-cable-6-ft.html) with your Soekris if you order online.
* Another machine with a spare serial port (I just use a Debian machine for this).

The method described below uses DHCP and TFTP to use PXE to boot your Soekris for the installation. This is one of a few available methods, see the [Soekris wiki](http://wiki.soekris.info/) for other possible methods. I personally prefer the below method as it's the simplest and uses tools you will most likely already have lying around.

### Preparing your Soekris

First you need to install everything into the case (if it's not pre-installed) and plug in your CF card. Grab a cross-over cable and connect eth0 on your Soekris to an available ethernet port on your extra machine.

### Preparing your DHCP server

You'll need another machine plugged into your Soekris with the null-modem cable. This machine needs to run both a DHCP server and a TFTP server so that we can PXE boot the Soekris to kick off the install. You will also require a serial terminal program so you can execute commands on the Soekris. I use cu since it's simple and generally just works, but minicom or any others should work just fine.

```console
$ aptitude install cu dhcpd3 tfptd openbsd-inetd
```

### Booting it up

Once you're happy with the above, plug in your Soekris' power cable and connect to the serial port.

```console
$ cu -l /dev/ttyS0 -s 19200
```

