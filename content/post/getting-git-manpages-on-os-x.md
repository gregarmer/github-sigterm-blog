+++
date        = "2010-04-15T12:00:00-04:00"
title       = "Getting Git man pages on OS X"
description = ""
tags        = [ "general", "mac", "git", "unix" ]
topics      = [ "general", "unix" ]
slug        = "getting-git-manpages-on-os-x"
+++

For some reason the OS X install of Git doesn't include the manpages. Here is how I installed them.

<!--more-->

First off, find the appropriate manpath.

```
greg@codemine:~ %> cat /etc/manpaths
/usr/share/man
/usr/local/share/man
```

/usr/local/share/man looks good...

```
greg@codemine:~ %> VER=`git --version | awk '{print $3}'`
greg@codemine:~ %> curl -O http://www.kernel.org/pub/software/scm/git/git-manpages-$VER.tar.bz2                              
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  242k  100  242k    0     0  92051      0  0:00:02  0:00:02 --:--:--   99k
greg@codemine:~ %> sudo tar xjv -C /usr/local/share/man -f git-manpages-$VER.tar.bz2   
Password:
x ./
x ./man1/
x ./man1/git-add.1
[snip]
x ./man7/gitworkflows.7
greg@codemine:~ %> rm git-manpages-$VER.tar.bz2    
greg@codemine:~ %>
```

"man git-add" should now work fine.
