+++
date        = "2019-08-11T17:53:00-04:00"
title       = "A walk through Nomad, Consul and Vault"
description = ""
tags        = [ "unix", "linux", "networking", "nomad", "consul", "docker" ]
topics      = [ "networking", "unix" ]
slug        = "containerize-all-the-things"
thumbnail   = "/static/containerize-all-the-things.jpg"
+++

A while ago I talked about [building a home lab] with ProxMox and a bunch of
hardware. That worked out great and served its purpose, however it was time to
move on into the world of containers, in a way that I could take back to my job
and run at an enterprise scale.

[building a home lab]: /2017/07/29/building-a-home-lab/

<!--more-->

I discovered the suite of [HashiCorp] products and dived head first into
setting up a [Nomad], [Consul] and [Vault]. This will cover deployment,
infrastructure-as-code (since I wanted to version everything), service
discovery and protected access to secrets (API tokens, credentials etc).

[HashiCorp]: https://www.hashicorp.com/
[Nomad]: https://www.nomadproject.io/
[Consul]: https://www.consul.io/
[Vault]: https://www.vaultproject.io/

##### The configuration

I'm running [Prometheus], Alertmanager, [snmp-exporter] and [blackbox-exporter]
in [Nomad] with [Consul] service discovery.  My Prometheus config for the
[blackbox-exporter] and [snmp-exporter] are below:

###### Blackbox Exporter Config

```yaml
scrape_configs:
  - job_name: 'blackbox'
    metrics_path: /probe
    params:
      module: [http_2xx]  # Look for a HTTP 200 response.
    static_configs:
      - targets:
        - https://sigterm.sh
        - https://lg.gxa.dn42.io
        - https://mx.gxa.io/ping/
        - https://nyc-1.us.gxa.dn42.io/api/bgp
        - https://sfo-1.us.gxa.dn42.io/api/bgp
        - https://fra-1.de.gxa.dn42.io/api/bgp
    relabel_configs:
      - source_labels: [__address__]
        target_label: __param_target
      - source_labels: [__param_target]
        target_label: instance
      - target_label: __address__
        replacement: blackbox-exporter.service.consul:9115
        # ^ The blackbox exporter's real hostname:port.
```

### The Final Result

Here are a few graphs I threw together demonstrating some SNMP data from my core
firewalls and switches, along with some temperature and relative humidity
graphs from my [pine64 project], a monitor for SMTP and IMAP connection latency
and finally some SSL certificate expiration monitoring.

![](/static/grafana-graphs.png)

[pine64 project]: https://github.com/gregarmer/pine64-temp-humidity
