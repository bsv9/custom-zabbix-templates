# Wazuh Agent via REST API
Overview
The template to monitor Wazuh Agent status that work without any external scripts. 

## Requirements
Zabbix version: 6.4 and higher.

## Macros used


| Name | Description | Default |
|---|---|---|
|{$WAZUH.API.URL}|The Wazuh API endpoint is a URL in the format `<scheme>://<host>:<port>`.|
|{$WAZUH.DATA.TIMEOUT}|A response timeout for the API.|10|
|{$WAZUH.HTTP.PROXY}|Sets the HTTP proxy to `http_proxy` value. If this parameter is empty, then no proxy is used.||
|{$WAZUH.USER}|The `user` of the Wazuh account. It is used to obtain an access token.||
|{$WAZUH.PASSWORD}|The `password` of the Wazuh account. It is used to obtain an access token.||
