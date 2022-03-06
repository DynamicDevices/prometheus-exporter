# Overview

This is a BalenHub 'Block' project to enable exporting of device and application metrics to Prometheus using the `prometheus-node-exporter`

[Prometheus](https://prometheus.io/docs/introduction/overview) is an open-source systems monitoring and alerting toolkit originally built at SoundCloud. Since its inception in 2012, many companies and organizations have adopted Prometheus, and the project has a very active developer and user community. It is now a standalone open source project and maintained independently of any company.

Prometheus scrapes key, value pair metric data from target systems. Here we run the `prometheus-node-exporter` which exposes a wide range of useful device and OS level data. In addition metrics can be exposed which are custom to running applications.

There are some details on configuring the `node-exporter` [here](https://prometheus.io/docs/guides/node-exporter) although this is already done by default in the Balena 'Block'

Background on what is exported by the `node-exporter` by default is `here`

# Block Configuration

Add the following to a `docker-compose.yml` for your fleet. At this time we expose the `node-exporter` on port 80 i.e. the public URL.

```
version: '2'

services:
  node_exporter:
    image: dynamicdevices/balena-prometheus-exporter
    restart: always
    privileged: false
    ports:
      - '80:9100'
```

With this Fleet configuration deployed you should be able browse to your public endpoint (on the balena device dashboard) and see a list of key value pairs of device metrics)

# Prometheus configuration

Install Prometheus on a cloud server of your choice and add a configuration to scrape the public URL of your device

For example append something of this form to the `/etc/prometheus/prometheus.yml` configuration and restart

```
  - job_name: balena-node
    scheme: https
    static_configs:
      - targets: ['name-of-your-public-endpoint.balena-devices.com']
```

Then restart. You should be able to browse to your Prometheus web interface on port `:9090` and check the targets. You should see that your `balena-node` is online.

# Grafana configuration

Install Grafana on a cloud server of your choice and add a DataSource of type Prometheus pointing at the host you are running your Prometheus installation on.

This will then automatically populate with the available data. You can also download some standard Grafana dashboards to display the `node-exporter` metrics

For more details see [here](https://prometheus.io/docs/visualization/grafana/)

The dashboard I used is [here](https://grafana.com/grafana/dashboards/1860)

![Dashboard Image](https://grafana.com/api/dashboards/1860/images/7994/image)

