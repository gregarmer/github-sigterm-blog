+++
date        = "2017-07-29T22:22:07-04:00"
title       = "dn42 Peering Information"
description = ""
tags        = []
topics      = []
slug        = "dn42-peering"
type        = "posts"
layout      = "single"
disable_comments = true
disable_authorbox = true
+++

If you are interested in peering with me on [dn42](https://dn42.net/Home), find the closest node(s) below and send the following to me in an e-mail to greg[at]sigterm.sh:

* AS Number:
* Tunnel IP:
* Public IP:
* IPv6 supported? (link-local preferred)

I prefer to use IPsec/GRE where possible, but let me know if you can't do it and we can work something out.

My ASN: **AS4242423602**<br/>
Network Name: **GXANET**

### Locations

- New York, NY, USA
- San Francisco, CA, USA
- Frankfurt, DE

### Tools

- [Looking Glass - Internet](https://lg.gxa.dn42.io/)
- [Looking Glass - DN42](https://lg.sigterm.dn42/)

### IPsec Parameters

```console
keyexchange=ikev2
ike=aes256-sha256-modp2048!
esp=aes256-sha256-modp2048!
ikelifetime=28800s
lifetime=3600s
```

### Nodes

#### nyc-1.us.gxa.dn42.io

* Location: New York, NY, USA
* Public IPv4: 159.203.180.81
* Public IPv6: 2604:a880:400:d0::8f7:1
* Tunnel IPv4: 172.20.194.70
* IPsec Public Key:

```console
-----BEGIN PUBLIC KEY-----
MIICIjANBgkqhkiG9w0BAQEFAAOCAg8AMIICCgKCAgEA2JI2A4TffozZplEoy8s3
6/UNc1L4LvNXEceUwIPHxfdEfvimkeHOHsh+WJC2OBAIw3pSCAP5EQPy1zc4sNvM
zLrwBdiS8v/KsNGBn7pAWmnDx7hnFUr2Rw6zqX4n8WzL7ewOUxb2qGu5BdwJvEqW
3VOdepm19AvAbU5SkeS5BsyTzjnBBvOs589EewMHtb5z15VlOg7TmKpFkw59HR/n
KKRuh0NhdTOp4gFU310JpIL7aK9etSr72/JjeBFe8JjgfXa6dj3Hf7WdT70hAAfc
b4EyLd8hfpb3k9FfKDrqLIomipsx8xMUVzpb9x7sRcP1ZK9s59/sRFiVJul5dMKQ
kMZh0L8GHtqE9IyEvYr8mixx1dwYZVwEp2qsdfcj02hubRtf6k6bEI/nfw+boszH
hdbuL7ChIQg+frt0YWIE8vxswq3SUQgSga8C3XR6J36CHfgGa4CYXDwmjT/tgT/J
E1WFzUZAnryGsFvrc0KsyI+1rT0LHxmKpydWiwVLBzPG59lbi+QjVEa284HmS81H
wIZ4NqWiCEzuvZeShD5sV+6mftt9/n+hcLi5ZfflJJ9i4Ci/sv/HLGvJV0rQiW2y
aob+uuOTNSWTt2/+mqaD8rySMttuGYa58LoybViFf4Hg1lhzdgpzZ81dV1Dowhts
UL2S4ZUBxcQGnO75UAFTZDUCAwEAAQ==
-----END PUBLIC KEY-----
```

#### sfo-1.us.gxa.dn42.io

* Location: San Francisco, CA, USA
* Public IPv4: 104.236.188.18
* Public IPv6: 2604:a880:1:20::dd9:5001
* Tunnel IPv4: 172.20.194.90
* IPsec Public Key:

```console
-----BEGIN PUBLIC KEY-----
MIICIjANBgkqhkiG9w0BAQEFAAOCAg8AMIICCgKCAgEAu3d3SAmnGW89loxS2DQi
+AgMYNTumxcRijzQIZCNHjzpfYMOQUAxos2OPE67nr24KQsl+m5wt6U2h0jXSQZh
sciyn9EXgZ6Uwc+BdUEH+sm+8JJCLh0De+GOb5dvnOeJupxUHN2P3iSUsdIt6dKS
nqCr0q5EN97LE51oqfkn9JMvI/XfpQaBezfUr2kfJyDMbAGbJUDQ4le7cIiZTlTE
e3xWH6cQ2UeGjjyn0ZK7e6wf1lhNoiFAbaRgv0CoPLbT8NTMVrw1kDENJfj3Enm6
BL+Et+lVu4L9vaCAMcy99zVBONaL5I2RWj7jgjpEZHBo8JX1RBuCrdGUf9JYvvpM
hPQm4hhUMGhlWwYcZMP3TZXrV2plUMvDmjzOrW6b51B3F98d438dWSkD6BlRjVSl
qLZt2Uipi3Kns+ascGwAKTE1l020O2n5rbB4/6IbsARqS3dGhUoaqBqfiRdF21U0
gtHmfM153dOnHvAwKSEB8NSdOa8wm+jcdCQTQ9EMQcq8zTmmgMFFrdhQAE5T/i70
aa2ZHW/XqjXY8NX8J1UBHsB9/mQXUQEdhcx/KK+QpwEAG4XpK8FdIthuotJrT+X8
bx28UEf3O8eh/SwNzvrW54yB6gT/7xL1HUtXCKeSMpa0Cjh61Dhl2PmfrZfYhWs9
lG/3jHWGHgwBf6Wm7h1mHEMCAwEAAQ==
-----END PUBLIC KEY-----
```

#### fra-1.de.gxa.dn42.io

* Location: Frankfurt, DE
* Public IPv4: 45.77.64.145
* Public IPv6: 2001:19f0:6c01:8df:5400:1ff:fe4a:a855
* Tunnel IPv4: 172.20.194.81
* IPsec Public Key:

```console
-----BEGIN PUBLIC KEY-----
MIICIjANBgkqhkiG9w0BAQEFAAOCAg8AMIICCgKCAgEAz9c27MPN9prCzcR0VQIg
ZBwH/IJl0O1tMrB+obd4zahV22NeQTVtbAHf7ZxSvr1XJemAjw286V7EQVBWh43n
PTx5ZQ9WncZ48RTs/GuSUpJGqUs9EaYa+Rd+wnRssXzWrmyD2FW87xgfLmgqCT9i
mafbLC1AauBBooNru7LFhpjpo/GNQDXfnxnMer+1EStnDtwoRk1OzbiStXS1REU4
Z4sCijFrAn8P823Vnfk0KY94ySYHGIIbfFmqoECvfAA+OqAYOJbmPO0zotnr30SY
6Atg1sqlA0hJCIRY/tv26Oh1U01u/WziFdfTx8kaooXVAIWk+JumEm+0jFc0d8PA
2vmnNkxfHB0rdByHh6jBgqRPcAydddPWni2QqA2dIcx6qBXO8+M8Rgblnwtq3kzx
T+lMHlofy23+HmQK7OtApTOJ8+ODZVJdEu/PQBBRB7thJ50r6uu5X8uwJ/Hygkwz
C5uNZBdjYww/MAs30fTZQdyo2VIc+FIRWkT6XFOU9Xlck89egen4j5ptV7tZGihc
7lqwWCy8nuiUG2+pwnDSYwtemxChlh155F6fjVMyhZJeqgI0A1CHpYlAoGKAHL81
gMYte/vZWDPPtNG9ile1AlCk5XM01gNgcxPTyt9+F9qRhU9ED63nI9Hb2pAnLBpD
FE4pSusk9FDNZAJu36XoO5sCAwEAAQ==
-----END PUBLIC KEY-----
```
