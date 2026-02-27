# Custom Zabbix Templates

A collection of ready-to-import Zabbix templates for monitoring infrastructure components. All templates are self-contained and require no external scripts unless noted otherwise.

## Templates

| Template | Method | Zabbix Version |
|---|---|---|
| [Traefik Metrics](traefik-metrics) | HTTP | 6.4+ |
| [Wazuh Agent](wazuh-agent) | REST API | 6.4+ |
| [Oxidized Backup Monitoring](oxidized) | HTTP | 7.0+ |
| [ESXi SMART Health](esxi-smart-health) | SSH | 7.2+ |
| [ESXi NVMe Health](esxi-nvme-health) | SSH | 7.2+ |
| [HPE Managed PDU](hpe-pdu) | SNMP | 7.4+ |

### Traefik Metrics

Monitor Traefik reverse proxy via its metrics endpoint — HTTP request rates, 4xx/5xx error counts. No external scripts needed.

### Wazuh Agent

Monitor Wazuh security agent status and health via the Wazuh REST API with basic authentication. Supports HTTP proxy configuration.

### Oxidized Backup Monitoring

Track network device configuration backups and detect changes in real time through the Oxidized REST API.

### ESXi SMART Health

Monitor HDD/SSD health on VMware ESXi hosts using S.M.A.R.T. attributes over SSH. Requires [smartmontools VIB](https://github.com/op7ic/smartmon-esxi) installed on the host and SSH key authentication.

### ESXi NVMe Health

Monitor NVMe device health on VMware ESXi hosts over SSH — media errors, temperature, spare capacity, endurance, and power cycles. No additional tools required on the host.

### HPE Managed PDU

Comprehensive SNMPv2c monitoring of HPE Managed PDUs: input power, per-outlet and per-phase energy tracking, breaker group status, and environmental sensors (temperature, humidity, contact/door).

## License

[MIT](LICENSE)
