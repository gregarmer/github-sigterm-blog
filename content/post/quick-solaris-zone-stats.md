+++
date        = "2009-08-13T12:00:00-04:00"
title       = "Quick Solaris zone stats"
description = ""
tags        = [ "unix", "opensolaris" ]
topics      = [ "unix", "opensolaris" ]
slug        = "quick-solaris-zone-stats"
+++

Add this:

`alias zonestat="prstat -vZ 1 1 | grep -A50 '^ZONEID'"`

to your ~/.profile and you should see something like this when running it:

```
root@tank:~# zonestat
ZONEID    NPROC  SWAP   RSS MEMORY      TIME  CPU ZONE
     0       58 1198M 1206M    30%  16:15:40 1.7% global
     6       25  172M  175M   4.4%   0:03:14 0.0% cl-build
     2       27   48M   31M   0.8%   0:00:47 0.0% mirror
Total: 110 processes, 534 lwps, load averages: 0.09, 0.08, 0.07
```
