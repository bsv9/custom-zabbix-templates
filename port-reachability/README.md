# Port Reachability

## Overview

This template checks TCP port reachability from the Zabbix agent host using the built-in `net.tcp.port` active check. It supports two modes: verifying that ports **should be reachable** (alerting when they go down) and verifying that ports **should NOT be reachable** (alerting when they respond). This is useful for validating firewall rules, confirming service availability from specific network segments, and detecting unintended exposure of internal services.

Targets are configured as simple CSV macros (`host:port` pairs). A JavaScript-based discovery rule parses the macros and creates the appropriate items and triggers automatically using LLD overrides.

## Requirements

- Zabbix version: 7.0 and higher
- Zabbix agent (active checks) running on the host performing the port checks

## Configuration

> Zabbix should be configured according to the instructions in the [Templates out of the box](https://www.zabbix.com/documentation/current/manual/config/templates_out_of_the_box) section.

## Setup

1. Link this template to the host that should perform the port checks

2. Set the `{$TCP_CHECKS}` macro to a comma-separated list of `host:port` pairs that should be reachable from this host:
   ```
   srv1:22,srv2:443,db01:5432
   ```

3. Set the `{$NOT_TCP_CHECKS}` macro to a comma-separated list of `host:port` pairs that should NOT be reachable from this host:
   ```
   db01:3306,internal:8080
   ```

4. Wait for the discovery interval (default 1h) or manually execute the discovery rule

### Macros used

|Name|Description|Default|
|----|-----------|-------|
|{$TCP_CHECKS}|CSV list of host:port that SHOULD be reachable, e.g. srv1:22,srv2:443|(empty)|
|{$NOT_TCP_CHECKS}|CSV list of host:port that should NOT be reachable, e.g. db01:3306,internal:8080|(empty)|
|{$TCP_CHECK_INTERVAL}|Interval for port reachability checks|5m|
|{$TCP_DISCOVERY_INTERVAL}|Interval for discovery of port checks|1h|

### Discovery rules

|Name|Description|Type|Key and additional info|
|----|-----------|----|-----------------------|
|TCP port check discovery|Discovers TCP port checks from {$TCP_CHECKS} and {$NOT_TCP_CHECKS} macros|Script|tcp.port.check.discovery|

### Item prototypes

|Name|Description|Type|Key and additional info|
|----|-----------|----|-----------------------|
|Port {#LABEL} check ({#TYPE})|Checks if port {#PORT} on {#HOST} is reachable|Zabbix agent (active)|net.tcp.port[{#HOST},{#PORT}]|

### Trigger prototypes

|Name|Description|Expression|Severity|Dependencies and additional info|
|----|-----------|----------|--------|--------------------------------|
|Port {#LABEL} is unreachable (should be reachable)|Port is not reachable but should be. Check firewall rules and remote service status.|last(/Port Reachability/net.tcp.port[{#HOST},{#PORT}])=0|Average|Enabled only when {#TYPE}=reachable|
|Port {#LABEL} is reachable (should NOT be reachable)|Port is reachable but should not be. Check firewall rules.|last(/Port Reachability/net.tcp.port[{#HOST},{#PORT}])=1|Average|Enabled only when {#TYPE}=not_reachable|

### Value maps

|Name|Mappings|
|----|--------|
|Service state|0 = Down, 1 = Up|
