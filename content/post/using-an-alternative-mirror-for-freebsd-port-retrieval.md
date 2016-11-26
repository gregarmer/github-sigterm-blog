+++
date        = "2009-10-12T16:00:00-04:00"
title       = "Using an alternative mirror for FreeBSD port retrieval"
description = ""
tags        = [ "unix", "freebsd" ]
topics      = [ "unix", "freebsd" ]
slug        = "using-an-alternative-mirror-for-freebsd-port-retrieval"
+++

This is something I always search for which doesn't seem to be very clear from the initial results. The mirrors included below are South Africa specific, so if you are not in South Africa then replace the hostname with something more appropriate for your location.

<!--more-->

Add these to **/etc/make.conf**:

```
MASTER_SITE_BACKUP?=    \
    ftp://ftp.za.freebsd.org/pub/FreeBSD/ports/distfiles/${DIST_SUBDIR}/

MASTER_SITE_OVERRIDE?=  ${MASTER_SITE_BACKUP}
```

#### Mirrors

<table class="table table-condensed" cellpadding="1" cellspacing="1" border="0">
  <tr>
    <td><strong>Network</strong></td>
    <td><strong>Hostname</strong></td>
    <td><strong>Alias</strong></td>
    <td><strong>IPv4</strong></td>
    <td><strong>IPv6</strong></td>
  </tr>
  <tr>
    <td>Tenet</td>
    <td>ftp.za.freebsd.org</td>
    <td>freebsd.mirror.ac.za</td>
    <td>155.232.191.209</td>
    <td>-</td>
  </tr>
  <tr>
    <td>CSIR</td>
    <td>ftp2.za.freebsd.org</td>
    <td>-</td>
    <td>146.64.8.4</td>
    <td>2001:4200:7000:1::4</td>
  </tr>
  <tr>
    <td>MTN</td>
    <td>ftp3.za.freebsd.org</td>
    <td>-</td>
    <td>196.30.227.198</td>
    <td>-</td>
  </tr>
  <tr>
    <td>IS</td>
    <td>ftp4.za.freebsd.org</td>
    <td>ftp.is.co.za</td>
    <td>196.4.160.12</td>
    <td>-</td>
  </tr>
</table>

See <a href="http://crnl.org/blog/2008/11/02/freebsd-local-ports-distfile-downloads-for-south-africans">this post</a> for a far more detailed set of MASTER_SITE make variables. This references a lot more of the locally mirrored content.
