+++
date        = "2011-01-02T12:00:00-04:00"
title       = "Puppet Modules - Debsecan"
description = ""
tags        = [ "debian", "puppet", "linux", "security" ]
topics      = [ "linux" ]
slug        = "puppet-modules-debsecan"
+++

This is the first post of (hopefully) many, detailing some of my <a href="http://www.puppetlabs.com/">Puppet</a> module implementations. Being the first, I thought I would start off with something simple.

<!--more-->

#### Debsecan

The <a href="http://www.enyo.de/fw/software/debsecan/">debsecan</a> program evaluates the security status of a host running the <a href="http://www.debian.org">Debian</a> operation system. It reports missing security updates and known vulnerabilities in the programs which are installed on the host.

This is a great package that I wanted installed on all <a href="http://www.debian.org">Debian</a> machines across my entire infrastructure. Thanks to Puppet, this is a breeze.

#### Module layout

```
greg@codemine:~/code/puppet %> find modules/debsecan
modules/debsecan
modules/debsecan/files
modules/debsecan/files/debsecan
modules/debsecan/files/debsecan-cron
modules/debsecan/manifests
modules/debsecan/manifests/init.pp
```

#### Manifest - init.pp

```
greg@codemine:~/code/puppet %> cat modules/debsecan/manifests/init.pp
class debsecan {
    package { debsecan: ensure => latest }

    file {
        debsecan:
            path => "/etc/default/debsecan",
            owner => root,
            group => "root",
            mode => 644,
            source  => "puppet:///debsecan/debsecan",
            require => Package["debsecan"];
        debsecan-cron:
            path => "/etc/cron.d/debsecan",
            owner => root,
            group => "root",
            mode => 644,
            source  => "puppet:///debsecan/debsecan-cron",
            require => Package["debsecan"];
    }
}
```

There is really not much to this manifest. It essentially ensures debsecan is installed at the latest available version, it sets up my /etc/default/debsecan config and it ensures there is a cron entry to run it.

#### Debsecan config

```
greg@codemine:~/code/puppet %> cat modules/debsecan/files/debsecan     
# Configuration file for debsecan.  Contents of this file should
# adhere to the KEY=VALUE shell syntax.  This file may be edited by
# debsecan's scripts, but your modifications are preserved.

# If true, enable daily reports, sent by email.
REPORT=true

# For better reporting, specify the correct suite here, using the code
# name (that is, "sid" instead of "unstable").
SUITE=lenny

# Mail address to which reports are sent.
MAILTO=root

# The URL from which vulnerability data is downloaded.  Empty for the
# built-in default.
SOURCE=
```

#### Debsecan cron

```
greg@codemine:~/code/puppet %> cat modules/debsecan/files/debsecan-cron
# cron entry for debsecan
MAILTO=root

42 * * * * daemon test -x /usr/bin/debsecan && /usr/bin/debsecan --cron
# (Note: debsecan delays actual processing past 2:00 AM, and runs only
# once per day.)
```

You can grab a copy of all the above files (the complete module) here: <a href='http://code.geek.sh/wp-content/uploads/2010/12/debsecan-puppet.tar.gz'>debsecan-puppet.tar.gz</a>
