(consul)=

# Consul datastore

<meta name="description" content="Use this Splunk Observability Cloud integration for the Consul datastore monitor. See benefits, install, configuration, and metrics">

## Description

The {ref}`Splunk Distribution of OpenTelemetry Collector <otel-intro>` provides this integration as the Consul datastore monitor with the SignalFx Smart Agent receiver.

Consul 0.7.0+ is supported.

```{note}
This monitor is not available on Windows as collectd plugins are only supported in Linux and Kubernetes. 
```

The integration monitors Consul datastores, and collects metrics from the following endpoints:

- [/agent/self](https://www.consul.io/api/agent.html#read-configuration)
- [/agent/metrics](https://www.consul.io/api/agent.html#view-metrics)
- [/catalog/nodes](https://www.consul.io/api/catalog.html#list-nodes)
- [/catalog/node/:node](https://www.consul.io/api/catalog.html#list-services-for-node)
- [/status/leader](https://www.consul.io/api/status.html#get-raft-leader)
- [/status/peers](https://www.consul.io/api/status.html#list-raft-peers)
- [/coordinate/datacenters](https://www.consul.io/api/coordinate.html#read-wan-coordinates)
- [/coordinate/nodes](https://www.consul.io/api/coordinate#read-lan-coordinates-for-all-nodes)
- [/health/state/any](https://www.consul.io/api/health.html#list-checks-in-state)

## Installation

The Splunk Distribution of OpenTelemetry Collector allows embedding a Smart Agent monitor configuration in an associated Smart Agent Receiver instance.

This monitor is available in the Smart Agent Receiver, which is part of the {ref}`Splunk Distribution of OpenTelemetry Collector <otel-intro>`.

Follow these steps to deploy the integration:

1. Deploy the Splunk Distribution of OpenTelemetry Collector to your host or container platform.
2. Complete the [configuration](https://docs.splunk.com/Observability/gdi/opentelemetry/configure-the-collector.html#otel-configuration).

## Configuration

The Splunk Distribution of OpenTelemetry Collector allows embedding a Smart Agent monitor configuration in an associated Smart Agent Receiver instance.

**Note:** Providing a Consul datastore monitor entry in your Collector or Smart Agent (deprecated) configuration is required for its use. Use the appropriate form for your agent type.

### Splunk Distribution of OpenTelemetry Collector

To activate this monitor in the OpenTelemetry Collector, add the following to your agent configuration:  

```
receivers:
    smartagent/consul:
        type: collectd/consul
        ...  # Additional config
```

To complete the monitor activation, you must also include the `smartagent/consul` receiver item in a `metrics` pipeline. To do this, add the receiver item to the `service` > `pipelines` > `metrics` > `receivers` section of your configuration file.

See <a href="https://github.com/signalfx/splunk-otel-collector/tree/main/examples" target="_blank">configuration examples</a> for specific use cases that show how the Splunk Distribution of OpenTelemetry Collector can integrate and complement existing environments.

If you are running a version of Consul earlier than 0.9.1, configure each Consul agent you want to monitor to send metrics to the OpenTelemetry Collector. To do so, add the following configuration to each Consul agent configuration file:

```
{"telemetry":
   {"statsd_address": "<agent host>:<agent port, default 8125>"}
}
```
### Smart Agent

To activate this monitor in the Smart Agent, add the following to your agent configuration:

```
monitors:  # All monitor config goes under this key
 - type: collectd/consul
   ...  # Additional config
```

If you are running a version of Consul earlier than 0.9.1, set the `telemetryServer` option to your agent configuration. This starts a UDP server listening on 0.0.0.0:8125 by default.

```
monitors:  # All monitor config goes under this key
   - type: collectd/consul
   telemetryServer: true
   ...  # Additional config
```

See <a href="https://docs.splunk.com/Observability/gdi/smart-agent/smart-agent-resources.html#configure-the-smart-agent" target="_blank">Smart Agent example configuration</a> for an autogenerated example of a YAML configuration file, with default values where applicable.

### Configuration settings

The following table shows the configuration options for this monitor:

| Option | Required | Type | Description |
| --- | --- | --- | --- |
| `pythonBinary` | no | `string` | Path to a python binary that should be used to execute the Python code. If not set, a built-in runtime will be used.  Can include arguments to the binary as well. |
| `host` | **yes** | `string` |  |
| `port` | **yes** | `integer` |  |
| `aclToken` | no | `string` | Consul ACL token |
| `useHTTPS` | no | `bool` | Set to `true` to connect to Consul using HTTPS. You can figure the certificate for the server with the `caCertificate` config option. (**default:** `false`) |
| `telemetryServer` | no | `bool` |  (**default:** `false`) |
| `telemetryHost` | no | `string` | IP address or DNS to which Consul is configured to send telemetry UDP packets. Relevant only if `telemetryServer` is set to `true`. (**default:** `0.0.0.0`) |
| `telemetryPort` | no | `integer` | Port to which Consul is configured to send telemetry UDP packets. Relevant only if `telemetryServer` is set to `true`. (**default:** `8125`) |
| `enhancedMetrics` | no | `bool` | Set to `true` to activate the collection all metrics from Consul runtime telemetry send using UDP or from the `/agent/metrics` endpoint. (**default:** `false`) |
| `caCertificate` | no | `string` | If Consul server has HTTPS activated for the API, specifies the path to the CA Certificate. |
| `clientCertificate` | no | `string` | If client-side authentication is activated, specifies the path to the certificate file. |
| `clientKey` | no | `string` | If client-side authentication is activated, specifies the path to the key file. |
| `signalFxAccessToken` | no | `string` |  |

## Metrics

These metrics are available for this integration.

<div class="metrics-yaml" url="https://raw.githubusercontent.com/signalfx/signalfx-agent/main/pkg/monitors/collectd/consul/metadata.yaml"></div>

### Notes

```{include} /_includes/metric-defs.md
```

## Get help

```{include} /_includes/troubleshooting.md
```
