+++
date        = "2011-12-06T12:00:00-04:00"
title       = "Replacing Django's Nasty 'runserver'"
description = ""
tags        = [ "software engineering", "django", "code", "python" ]
topics      = [ "code" ]
slug        = "replacing-djangos-nasty-runserver"
+++

Have you ever tried to have more than one person view a development site using <a title="Django" href="http://www.djangoproject.com/" target="_blank">Django's</a> <a title="Django runserver" href="https://docs.djangoproject.com/en/1.3/ref/django-admin/#runserver-port-or-address-port" target="_blank">built-in development server</a> ? Yeah, it really sucks. Apparently concurrency wasn't high on the features list and they have stated that it never will be.

<!--more-->

> DO NOT USE THIS SERVER IN A PRODUCTION SETTING. It has not gone through security audits or performance tests. (And that's how it's gonna stay. We're in the business of making Web frameworks, not Web servers, so improving this server to be able to handle a production environment is outside the scope of Django.)

So how do we go about using something a little nicer without losing any of the auto-reload goodness and without having to setup a full blown production environment ?

There are a number of alternatives, however I've selected <a title="Twisted Web" href="http://twistedmatrix.com/documents/current/web/howto/web-in-60/index.html" target="_blank">Twisted Web</a> simply because I really like the <a title="Twisted" href="http://twistedmatrix.com/trac/" target="_blank">twisted framework</a> and due to the experience I have in using it, I am very comfortable with it. It's a great feature-packed web server that handles concurrency (and a ton of other things) exceptionally well.

So how do we use it to serve our little Django project in a development friendly way ?

I've put together some code (some borrowed from other sources) and constructed a simple replacement command called "trunserver" (twisted-runserver). You can grab this code from <a title="GitHub - trunserver" href="https://github.com/gregarmer/trunserver" target="_blank">Github</a>. Simply install it using the standard methods, and run it with:

```console
python manage.py trunserver [IP:PORT] [--settings=foo] [--noreload]
```

So this will start up a twisted web instance serving your Django project and just like the build-in runserver, it will automatically reload your code when it notices that your files have been modified unless --noreload has been passed.

There are a few things missing at this point, like IPv6 support and static file serving, however these are on the roadmap.

I'll post again with some more info once it is a little more stable and an official release has been provided.
