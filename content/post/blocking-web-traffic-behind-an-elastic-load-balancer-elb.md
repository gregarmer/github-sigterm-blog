+++
date        = "2012-09-28T12:00:00-04:00"
title       = "Blocking web traffic behind an Elastic Load Balancer (ELB)"
description = ""
tags        = [ "linux", "amazonaws", "unix", "security" ]
topics      = [ "unix", "amazonaws" ]
slug        = "blocking-web-traffic-behind-an-elastic-load-balancer-elb"
+++

Over the past few hours we've been on the receiving end of a fairly large scale set of web requests (read: attack) to a website we host over on Amazon EC2. Our setup is not really that complicated, however we encountered a problem that wasn't that easy to solve.

<!--more-->

#### Our Setup

So we have an [Elastic Load Balancer][1] out in front, that sends on web requests to a set of web servers. These web servers are in an auto-scaling group and simply run [nginx][2]. They then pass traffic onto a set of load balanced application servers. Nothing really out of the ordinary here.

The entire setup lives inside a [VPC][3] so these server have no direct access to the internet, and specific traffic is NAT'd out another gateway.

#### The Problem

We narrowed down the attack to a set of IPs all originating from the same netblock. These requests were largely made up of SQL injection attacks, along with some other crazy requests.

The requests looked like this:

    208.96.18.11 "GET /results/2011%27%20OR%20%271%27%3D%271/11/ HTTP/1.1" "Mozilla/5.0"
    208.96.18.11 "GET /results/2011)%20OR%201%3D(1/11/ HTTP/1.1" "Mozilla/5.0"
    208.96.18.11 "GET /results/2011%27)%20OR%20%271%27%3D(%271/11/ HTTP/1.1" "Mozilla/5.0"
    208.96.18.11 "GET /results/2011%27%20OR%201%3D1%20%23/11/ HTTP/1.1" "Mozilla/5.0"
    208.96.18.11 "GET /results/2011%27)%20AND%20%271%27%20in%20(%270/11/ HTTP/1.1" "Mozilla/5.0"

and without the URL encoding:

    208.96.18.11 "GET /results/2011' OR '1'='1/11/ HTTP/1.1" "Mozilla/5.0"
    208.96.18.11 "GET /results/2011) OR 1=(1/11/ HTTP/1.1" "Mozilla/5.0"
    208.96.18.11 "GET /results/2011') OR '1'=('1/11/ HTTP/1.1" "Mozilla/5.0"
    208.96.18.11 "GET /results/2011' OR 1=1 #/11/ HTTP/1.1" "Mozilla/5.0"
    208.96.18.11 "GET /results/2011') AND '1' in ('0/11/ HTTP/1.1" "Mozilla/5.0"

*(Yes, that's one of the actual IPs attacking us.)*

So the obvious move here is to block traffic from the offending IPs, right ?

Unfortunately that's simply not possible with an ELB, as the only control you have over the traffic is through a [security group][4]. However, the security group only lets you control *allow* rules, so to block a specific IP you'd need to explicitly allow traffic from all other IPs on the internet. This is simply not feasible.

You can't block the traffic with a firewall on the web servers either, as the traffic hitting the web servers has the source IP address of the load balancer. The only access you have to the real source IP at the web server level is in the X-Forwarded-For HTTP header inside that actual requests.

#### Blocking the Traffic

The best you can do in this scenario is to block all requests at the actual web servers. In our case on nginx this was as simple as adding the following directives to the *server* block:

    set_real_ip_from 172.16.10.9;         // Our ELB
    real_ip_header X-Forwarded-For;       // The real IP from the ELB
    deny 208.96.18.11;                    // Have some HTTP 403

This doesn't stop the traffic at all, it simply returns HTTP 403's to any requests from the IP addresses or netblock listed. This does take the load off the application servers though, and since nginx is quite efficient at returning 403's with little resource usage, we can serve them up nice and quickly. It would still be nice to firewall that traffic entirely, and hopefully, someday, Amazon adds the ability to configure *allow* __and__ *deny* rules in security groups.

[1]: http://aws.amazon.com/elasticloadbalancing/ "Elastic Load Balancer"
[2]: http://nginx.org/en/ "nginx"
[3]: http://aws.amazon.com/vpc/ "VPC"
[4]: http://docs.amazonwebservices.com/AWSEC2/latest/UserGuide/using-network-security.html "Security Groups"
