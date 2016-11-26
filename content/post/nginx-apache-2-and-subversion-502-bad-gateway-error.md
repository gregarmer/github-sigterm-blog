+++
date        = "2012-10-09T12:00:00-04:00"
title       = "nginx, Apache 2 and subversion - 502 Bad Gateway error"
description = ""
tags        = [ "software engineering", "code" ]
topics      = [ "code" ]
slug        = "nginx-apache-2-and-subversion-502-bad-gateway-error"
+++

#### The Problem

I recently ran into this problem and couldn't find any useful information on the net around fixing it. All subversion checkouts, commits and other basic operations work just fine, but when attempting to copy, move or tag (copy) I would get the below (502 Bad Gateway) error.

<!--more-->

```console
greg@codemine:~/code/Foo (git-svn)-[trunk] %> git svn tag foo-2.1.1
Copying https://svn/projects/Foo/trunk at r18311 to https://svn/projects/Foo/tags/foo-2.1.1...
RA layer request failed: Server sent unexpected return value (502 Bad Gateway) in response to COPY request for '/projects/!svn/bc/18311/Foo/trunk' at /usr/libexec/git-core/git-svn line 1123
```

#### Our Setup

We have a machine running nginx on port 80 (and 443 SSL) that serves up a bunch of development tools - buildbots, Jenkins, git repos and of course a few subversion repos. Behind it we have an Apache2 server that serves up subversion repos over WebDAV.

nginx simply reverse proxies requests to the backend Apache 2 servers and handles the SSL termination as our repos are only available over SSL (https).

#### What's Really Happening

The actual problem is not really that obvious.

When you perform a remote svn operation like MOVE or COPY the actual request is translated into a WebDAV request that looks similar to this:

```console
COPY /svn/repos/oldname.txt HTTP/1.1
Host: svn.example.org
Destination: https://svn.example.org/svn/repos/newname.txt
```

Apache, the webserver, translates the above request to:

```console
COPY https://svn.example.org/svn/repos/oldname.txt https://svn.example.org/svn/repos/newname.txt
```

It uses the Host parameter *svn.example.org* and the first request line `COPY
/svn/repos/oldname.txt HTTP/1.1` to assemble the source URL,
`https://svn.example.org/svn/repos/oldname.txt`.

However, since requests are being reverse proxied through nginx, the source URL is changed from https:// to http:// as Apache listens on port 80 (HTTP). This results in a COPY operation that looks like this:

```console
COPY http://svn.example.org/svn/repos/oldname.txt https://svn.example.org/svn/repos/newname.txt
```

*Note the http:// instead of https://*

Apache quickly figures out that it can't move a file http://svn.example.org/svn/repos/oldname.txt to https://svn.example.org/svn/repos/newname.txt, because as far as Apache is concerned http://svn.example.org/ and https://svn.example.org/ are two entirely different hosts.

... and you end up with a "502 Bad Gateway" error.

#### The Solution

The solution to this problem is getting nginx to change the Destination header in the same way it changes the Host header. Apache can then handle the COPY or MOVE operation correctly. This is done by adding the following to your nginx configuration:

```nginx
set $fixed_destination $http_destination;
if ( $http_destination ~* ^https(.*)$ ) {
    set $fixed_destination http$1;
}
proxy_set_header Destination $fixed_destination;
```

#### Complete Configs

For reference purposes I've included the complete configs below.

*/etc/nginx/sites-available/svn.example.org*

```nginx
server {
    listen 80;
    server_name svn.example.org;
    rewrite ^(.*) https://svn.example.org$1 permanent;
}

server {
    listen 443;
    server_name svn.example.org;

    ssl on;
    ssl_certificate /etc/nginx/conf.d/svn.example.org.crt;
    ssl_certificate_key /etc/nginx/conf.d/svn.example.org.key;
    ssl_verify_depth 3;

    client_max_body_size 100m;
    access_log /var/log/nginx/svn.example.org-access.log;

    location / {
        set $fixed_destination $http_destination;
        if ( $http_destination ~* ^https(.*)$ ) {
            set $fixed_destination http$1;
        }
        proxy_set_header Destination $fixed_destination;
        proxy_set_header Host $http_host;

        proxy_pass http://localhost:9080;
        proxy_connect_timeout 75;
        proxy_read_timeout 300;
        proxy_send_timeout 300;
    }
}
```

*/etc/apache2/sites-available/svn.example.org*

```apacheconf
<VirtualHost *:9080>
    ServerName svn.example.org
    ErrorLog /var/log/apache2/svn.example.org-error.log
    <Location />
        DAV svn
        DAVMinTimeout 0
        SVNParentPath /home/svn
        AuthType Basic
        AuthName "Subversion Repos"
        AuthUserFile /home/svn/.htpasswd
        AuthzSVNAccessFile /home/svn/conf/authz
        Require valid-user
    </Location>
</VirtualHost>
```
