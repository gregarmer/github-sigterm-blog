+++
date        = "2017-07-29T22:22:07-04:00"
title       = "dn42 Peering Information"
description = ""
tags        = []
topics      = []
slug        = "dn42-peering"
disable_comments = true
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

### IPsec Parameters

```
keyexchange=ikev2
ike=aes256-sha256-modp2048!
esp=aes256-sha256-modp2048!
ikelifetime=28800s
lifetime=3600s
```

### Nodes

#### gxa-nyc-01.sigterm.dn42

* Location: New York, NY, USA
* Public IPv4: 159.203.180.81
* Public IPv6: 2604:a880:400:d0::8f7:1
* Tunnel IPv4: 172.20.194.70
* IPsec Public Key:

```
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

#### gxa-sfo-01.sigterm.dn42

* Location: San Francisco, CA, USA
* Public IPv4: 104.236.188.18
* Public IPv6: 2604:a880:1:20::dd9:5001
* Tunnel IPv4: 172.20.194.90
* IPsec Public Key:

```
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
