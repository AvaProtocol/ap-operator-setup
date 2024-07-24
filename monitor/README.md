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

3.a If you are running the mainnet, run the following command to
   connect your mainnet node network to the monitoring network:

    ```bash
    docker network connect ava_ethereum prometheus
    ```

3.b If you are running the holesky, run the following command to
   connect your node network to the monitoring network:

    ```bash
    docker network connect ava prometheus
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
and can be visualize with Grafana.

If you run your own grafana and/or prometheus, you can refer to the file
`prometheus.yml` on configuring prometheus target and `dashboards` folder to import
our pre-define dashboard for core metrics.

## Metrics Definitions

To help you configure alerts, we provide definitions of metrics below:

- **ap_uptime_milliseconds_total**: Total uptime in milliseconds of the operator
  since boot. The counter resets to 0 and counts up if your operator reboots.
You can use `increase` or `rate` to calculate the sum/rate and alert. Ideally, if
you group by hour, the sum will be ~3600s. If the hourly uptime is 10% less than
that, it indicates that the operator may not get enough CPU time. Consider
allocating more cores if you're using resource constraints.

- **ap_num_tasks_received**: How many tasks your operator has received. If the
  rate of a 5-minute window drops to 0, you know the operator has not received
  tasks and it should be alerted. This can either be because the operator failed
  to establish a connection with the aggregator, or the aggregator failed to push
  the message. Check out the [telemetry page](https://aggregator.avaprotocol.org/telemetry)
  for any issue or reach out to us on telegram.

- **ap_num_worker_loop_total**: The operator has a work loop. If this counter
  doesn't increase in a 5-minute window, it should be alerted. It means your
  operation has stalled. Check the log; it should have a sign to fix the issue.

- **ap_ping_duration_seconds**: The ping latency between the operator and
  aggregator. This depends on your network traffic, but ideally, it should be
  less than 5 seconds. If it's higher than that, it can indicate degraded
  performance on your node, your network, or our aggregator. If CPU and network
  bandwidth look good on your end, ping us on Telegram or check our status page
  (coming soon).

### What to do when the metrics is in alert status

First off, check to ensure your node has enough free CPU and memory to allocate
to the operator to do the work. Ensure traffic is flowing between your node and
our aggregator. That means outgoing port 2206 is opened.

Check your node log; it will have issues such as the node being stuck in a crash
loop, indicating the root cause of the error there.

It could be because of an RPC on your node, preventing the node from running, or
the key being removed, causing the node to fail to run.

If there is no error level log, it's most likely an issue with the aggregator.
We will update our status page(coming soon) and telemetry page to indicate these.
