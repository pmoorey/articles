# Building a Network Monitoring Agent using Google Cloud

## Overview

This article provides a super basic framework for building a remote network monitoring agent and using Google Cloud to ingest the data, visualize it and manage the overall solution.  The goal for building this was to learn about various cloud-native features in Google Cloud.

## Objectives

Here are the deliverables for this project:
1. Run a process on a _remote agent_ to gather network performance metrics
2. Manage the agent operations from a central console (start/stop/update)
3. Store and visualize data to show historical performance

## Architecture Diagram

The diagram below highlights the different systems and functions used to create the solution.
![Tracing architecture diagram](https://github.com/pmoorey/articles/blob/master/img/network-monitoring-agent/architecture.png)

## Remote Agent

The agent will collect data about network performance using ICMP probes, but this could be extended to any idea imaginable.  To make the agent portable I constructed a Docker image with Python and a few essential packages.  The container executes a Python script which starts the agent, continuously collects network performance data, and sends it to Google Cloud for processing.

_Probe functionality_

```
while True:

    if device.config['agent_operations']["monitoring_enabled"] is True:

        for probe in device.config['probes']:

            ip = probe['ip_address']
            name = probe['name']
            probe_type = probe['type']

            if probe_type == "icmp":
                dt = datetime.datetime.now()
                response_list = ping(ip, size=40, count=4)
                avg_latency = int(response_list.rtt_avg_ms)
                min_latency = int(response_list.rtt_min_ms)
                max_latency = int(response_list.rtt_max_ms)

                payload = json.dumps({'avg_latency': avg_latency,
                                        'min_latency': min_latency,
                                        'max_latency': max_latency,
                                        'timestamp': int(dt.strftime("%s")),
                                        'ip_address': ip,
                                        'name': name})

                print('Publishing payload', payload)
                client.publish(mqtt_telemetry_topic, payload, qos=1)

    # pauses five seconds.
    time.sleep(int(device.config['agent_operations']['probe_interval']))

client.disconnect()
client.loop_stop()
```
