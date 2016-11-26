+++
date        = "2014-10-26T05:53:00-04:00"
title       = "SSL Configuration"
description = ""
tags        = [ "linux", "security" ]
topics      = [ "linux" ]
slug        = "ssl-configuration"
banner      = "https://media.sigterm.sh/2015/Screen-Shot-2014-10-26-at-5-34-16-AM.png"
+++

As you may have noticed, this site is now served over HTTPS!

IE6 users, you're pretty much SOL since I turned off all your cipher suites. It's 2014 and it's probably a pretty good time to get yourself a slightly newer browser anyway.

<!--more-->

For anyone interested, here's the NGINX config I'm using:

```nginx
server {
    listen 80;
    server_name sigterm.sh;
    rewrite ^(.*) https://sigterm.sh$1 permanent;
}

server {
    listen 443 ssl;
    server_name sigterm.sh;
    ssl_certificate certs/sigterm.sh.crt;
    ssl_certificate_key certs/sigterm.sh.key;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;
    ssl_session_timeout 5m;
    ssl_ciphers "EECDH+ECDSA+AESGCM EECDH+aRSA+AESGCM EECDH+ECDSA+SHA384 \
        EECDH+ECDSA+SHA256 EECDH+aRSA+SHA384 EECDH+aRSA+SHA256 EECDH+aRSA+RC4 \
        EECDH EDH+aRSA RC4 !aNULL !eNULL !LOW !3DES !MD5 !EXP !PSK !SRP !DSS";

    gzip on;
    gzip_min_length 2000;
    gzip_proxied any;
    gzip_types application/json;

    location / {
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_pass http://127.0.0.1:1234;
    }
}
```

You can check out the full test report over at [SSL Labs](https://www.ssllabs.com/ssltest/analyze.html?d=sigterm.sh).
