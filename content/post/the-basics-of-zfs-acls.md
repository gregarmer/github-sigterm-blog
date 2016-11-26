+++
date        = "2009-07-31T12:00:00-04:00"
title       = "The basics of ZFS ACLs"
description = ""
tags        = [ "zfs", "unix", "opensolaris" ]
topics      = [ "unix", "opensolaris" ]
slug        = "the-basics-of-zfs-acls"
+++

This post was mostly inspired by reading <a href="http://www.1stbyte.com/2009/07/24/zfs-cifs-and-acl-inheritance/">this post</a> in trying to get my head around the ZFS ACL and permission system.

<!--more-->

Basically I have a pool set out as follows:

```
tank
tank/media
tank/zones
```

tank/media is served via CIFS and NFS to multiple clients on my network, each with their own unix account on the OpenSolaris server. tank/zones is used for extra zones running on the host.

Everything was working great until I found that files and directories created by clients ended up looking like this:

```
----------  1 greg staff 734310400 2009-07-18 19:10 foo.txt
d---------  2 greg staff        19 2008-12-06 14:10 Bar
```

This sure didn't go down well when other users needed to access those files or directories.

So in following the above mentioned post I did this:
```
# zfs set aclinherit=passthrough tank/media
# zfs set aclmode=passthrough tank/media
# /bin/chmod 0774 /tank/media
# /bin/chmod -R A- /tank/media
# /bin/chmod -R A=owner@:full_set:fd:allow /tank/media
# /bin/chmod -R A+group@:full_set:fd:allow /tank/media
# /bin/chmod -R A+everyone@:read_set:fd:allow /tank/media
```

A better description of what the flags / syntax mean can be found <a href="http://dlc.sun.com/osol/docs/content/ZFSADMIN/gbace.html">here</a> and <a href="http://dlc.sun.com/osol/docs/content/ZFSADMIN/gbacb.html">here</a>

A simple breakdown:

* First off, we tell ZFS that all files or directories must inherit all acls / permissions from their parent.
* We use /bin/chmod as the chmod in the default path is the GNU chmod which does not understand ZFS acls.
* The second chmod "A-" will remove all acls currently set on the object.
* We then set the owners permission to the "full_set", thus giving the owner all possible permissions.
* We do the same for the group.
* Finally, we give everyone else read access.
