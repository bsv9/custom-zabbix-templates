# Traefik Metrics
The template to monitor Traefik Metrics by Zabbix that work without any external scripts.

## Requirements
Zabbix version: 6.4 and higher.

## Macros used


| Name | Description | Default |
|---|---|---|
|{$TRAEFIK.API}|The Traefik Metrics endpoint e.g. http://localhost:8002`.|
|{$TRAEFIK.4XX_ERRORS}|4xx requests per 5 minute|0|
|{$TRAEFIK.5XX_ERRORS}|4xx requests per 5 minute|0|
