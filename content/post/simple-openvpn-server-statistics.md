+++
date        = "2009-07-16T12:00:00-04:00"
title       = "Simple OpenVPN Server Statistics"
description = ""
tags        = [ "code", "python" ]
topics      = [ "code", "python" ]
slug        = "simple-openvpn-server-statistics"
+++

Ever wondered what the status of your OpenVPN server is, or wanted some simple stats ?

<!--more-->

Here is a really simple script to give you some info. The output looks like this:

```console
greg@leonis:~$ vpnstatus
Common Name   Virtual Address    Real Address          Sent      Received             Connected Since
greg.vpn      10.8.142.6         196.9.23.59        1.11 MB     282.50 KB    Thu Jul 16 09:07:15 2009
```

Firstly, ensure your server is actually saving the stats somewhere (/etc/openvpn/openvpn.status in my example):

```console
greg@leonis:~$ grep status /etc/openvpn/server.conf
status openvpn.status
```

Once that is happening, drop this code into a file and make it executable.

<script src="https://gist.github.com/gregarmer/5a6c096be858580da889.js"></script>
