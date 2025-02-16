# Configuration Backup Monitoring via Oxidized
A template for monitoring configuration backups via Oxidized. Also notify when changes are made to the configuration.”

## Requirements
Zabbix version: 7.0 and higher.

## Macros used

| Name | Description | Default |
|---|---|---|
|{$OXIDIZED.BASE_URL}|Base Oxidized URL in the format `<scheme>://<host>:<port>`.|
|{$OXIDIZED.NAME.MATCHES}|Filter of items by name||
|{$OXIDIZED.NAME.NOT_MATCHES}|Filter of items by name|CHANGE_IF_NEEDED|
|{$OXIDIZED.PASSWORD}|Password for basic authn||
|{$OXIDIZED.USERNAME}|Username for basic authn||

