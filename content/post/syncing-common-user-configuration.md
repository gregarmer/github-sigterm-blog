+++
tags = [
	"unix",
	"linux",
]
categories = [
]
description = ""
menu = ""
banner = ""
images = [
]
draft = true
date = "2016-12-03T11:59:20-05:00"
title = "Syncing common user configuration"

+++

So, I look after a bunch of servers, on some I have bash set as my login shell,
on others I have zsh. There are a few where I use vim and others where vi is
the default. Some are Linux, some are OpenSolaris, others are BSD. The list of
differences goes on and on.

<!--more-->

The problem I have is that I like my account setup in a certain way and every
time I make a change to some or other config I need to go sync up (and
sometimes tweak) this change across all servers. I've spent countless hours
perfecting my .muttrc, .vimrc, .zshrc and many, many other configuration files,
and keeping these in sync requires a large amount of effort. I also prefer that
all my configurations are under some form of version control.

So here is what I came up with to make this process less painful.

Firstly, all configs are stored in subversion. Using git, darcs, cvs or any
other version control system would work just fine.

The directory structure I use goes like this. You have a `trunk/configs/base/`
directory where all your configs go. You also have one directory for each
operating system you are targeting, for example:

```console
 trunk/configs/FreeBSD/
 trunk/configs/OpenBSD/
 trunk/configs/SunOS/
 trunk/configs/Linux/
```

It is important to name it the same as the output of "uname -s".

Get your configurations to how you want them and put them all in the base directory. At the end of each configuration file add the following block of text, add one for each operating system you want.

```console
%FreeBSD%
%OpenBSD%
%SunOS%
%Linux%
```

These will be replaced by the replacements we are going to do next.

