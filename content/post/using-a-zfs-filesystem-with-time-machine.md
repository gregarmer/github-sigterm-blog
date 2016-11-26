+++
date        = "2009-10-04T12:00:00-04:00"
title       = "Using a ZFS filesystem with Time Machine"
description = ""
tags        = [ "zfs", "mac", "unix", "opensolaris" ]
topics      = [ "unix", "opensolaris" ]
slug        = "using-a-zfs-filesystem-with-time-machine"
+++

This simple how-to explains how to get your Time Machine backups working with a ZFS filesystem. This allows you to use the features of ZFS filesystems for your Time Machine backups.

**Please note this is for Mac OS X - Snow Leopard.**

<!--more-->

1)    Enable unsupported network volumes on your Mac by opening a Terminal and pasting this:

```
greg@macbook:~ %> defaults write com.apple.systempreferences TMShowUnsupportedNetworkVolumes 1
```

2)    Create a new ZFS filesystem and enable CIFS access to it:

```
greg@opensolaris:~ %> zfs create tank/userbackups
greg@opensolaris:~ %> zfs set sharesmb=on tank/userbackups
greg@opensolaris:~ %> zfs set sharesmb=name=userbackups tank/userbackups
greg@opensolaris:~ %> zfs set aclmode=passthrough tank/userbackups
greg@opensolaris:~ %> zfs set aclinherit=passthrough tank/userbackups
```

You will probably want to setup the correct permissions on your new share, more details in <a href="http://code.geek.sh/2009/07/the-basics-of-zfs-acls/">[this post]</a>.

3)    Make sure you can mount this share and write to it from your Mac.

4)    Create the correct disk image:

```
greg@macbook:~ %> /bin/bash
greg@macbook:~ %> cd /Volumes/userbackups
greg@macbook:~ %> SYSNAME=`scutil --get ComputerName`
greg@macbook:~ %> hdiutil create -size 600G -fs HFS+J \
> -volname 'Time Machine Backups' -type SPARSEBUNDLE "${SYSNAME}.sparsebundle"
greg@macbook:~ %> UUID=`system_profiler | grep 'Hardware UUID' | awk '{print $3}'`
greg@macbook:~ %> cat << EOF > "${SYSNAME}.sparsebundle"/com.apple.TimeMachine.MachineID.plist
> <?xml version="1.0" encoding="UTF-8"?>
> <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
> <plist version="1.0">
> <dict>
>         <key>com.apple.backupd.HostUUID</key>
>         <string>$UUID</string>
> </dict>
> </plist>
> EOF
greg@macbook:~ %>
```

5)    and finally, open up Time Machine. You should now see your network share as an option. Choose it, configure any excludes you want and kick off your first backup!

I'll post a little later on restoring these backups using one of these methods:

<ul style="padding-left: 30px;">
	<li>Restore from Time Machine by using the boot disk</li>
	<li>or by doing a standard install then using the Migration Assistant.</li>
</ul>

Good luck!
