+++
date        = "2019-08-11T12:37:00-04:00"
title       = "Some fun with monitoring"
description = ""
tags        = [ "unix", "linux", "networking", "nomad", "monitoring" ]
topics      = [ "networking", "unix" ]
slug        = "some-fun-with-monitoring"
thumbnail   = "/static/sigterm-site-monitor.png"
+++

I recently switched my home lab from ProxMox VMs to HashiCorp [Nomad],
[Consul], [Vault] and containers running on bare metal servers, and I needed
a way to monitor this stack, my applications as well as external systems that
I care about.

[Nomad]: https://www.nomadproject.io/
[Consul]: https://www.consul.io/
[Vault]: https://www.vaultproject.io/

<!--more-->

##### The Monitoring Stack

I looked at a bunch of options and finally settled on [Prometheus], [Grafana]
and some other smaller exporters that could scrape the data I needed and
present it in a simple way.

[Prometheus]: https://prometheus.io/docs/introduction/overview/
[Grafana]: https://grafana.com/

###### Exporters

If you're not familiar with how [Prometheus] works, the basic essential you
need to know is that [Prometheus] servers will scrape targets periodically to
collect metrics. This is a problem if you need to scrape data from a network
device that only supports SNMP, or you need connection metrics for a website
where you can't install more detailed web server metrics exporters. I can't
install a Prometheus exporter on my Juniper firewall for example, well I
probably could since it's a BSD OS underneath but that would most likely mean
a lot of pain during software upgrades. However, there are two exporters that
can help out here:

* [snmp-exporter] - Act as a proxy and scrape SNMP data from a device
* [blackbox-exporter] - Act as a proxy and scrape HTTP (and other) metrics from
 a url

![](/static/sigterm-site-monitor.png)

[snmp-exporter]: https://github.com/prometheus/snmp_exporter
[blackbox-exporter]: https://github.com/prometheus/blackbox_exporter
