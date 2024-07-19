## Table of Contents <!-- omit in toc -->
- [Introduction](#introduction)
- [Setup](#setup)
- [Dashboards](#dashboards)

## Introduction

This guide covers how to setup the monitoring docker containers for Ava Protocol
AVS.

It contains a full stack monitoring with prometheus, grafana, and data is store
in docker volume. If you already had an existing grfana/prometheus stack, you
can extract or use this as your sample implementation for metric scraper and
dashboard definition


## Setup

1. Change your shell current working directory to this folder.

2. Update your secret


```
# make a copy of .env.example
cp .env.example .env
```

Edit your `.env` to update 3 things

```
# This will be the port to access your grafana
GRAFANA_PORT=3009
# this is the default admin user name
GF_SECURITY_ADMIN_USER=admin
# this is the default admin user password
GF_SECURITY_ADMIN_PASSWORD=fluffybunny
```

Those are initially credentiall, after grafana is up, you can also change the
password directly on the UI.


2. Now, run the following command to start the monitoring stack:

```bash
docker compose up -d
```


3.a If you are running the holesky, run the following command to
   connect your node network to the monitoring network:

```bash
docker network connect ava-protocol-network prometheus
```

3.a If you are running the mainnet, run the following command to
   connect your mainnet node network to the monitoring network:

```bash
docker network connect ava-protocol--network prometheus
```

4. Done! Now Prometheus should be scraping the metrics from the AP avs
   container that you have setup. Give it a few minutes and you should also be
able to see some metrics in your Grafana dashboards.


## Dashboards
By default, Ava Protocol AVS provides a set of Grafana dashboards
[here](./dashboards/), which are automatically imported if you followed the
steps above. These can be used to quickly see the health of your Ava Protocol
AVS container and whether there are any problems.

1. Open in the browser http://<your-node-ip>:<GRAFANA_PORT-in-your-env-file>
2. Enter the username password you set in your config file


Our operator expose prometheus metric. These metric can be scrape by prometheus
and can be visualize with Grafana

If you run your own grafana and/or prometheus, you can refer to the file
`prometheus.yml` on configuring prometheus target and `dashboards` folder to import
our pre-define dashboard for core metrics.
