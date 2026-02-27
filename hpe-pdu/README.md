# HPE Managed PDU by SNMP

## Overview

This template provides comprehensive SNMP-based monitoring for HPE Managed (Metered and Switched) Power Distribution Units. It collects device identification, input power metrics, per-phase/section electrical data, per-outlet power consumption, breaker group status, and environmental sensor readings (temperature, humidity, contact/door sensors) using the CPQPOWER MIB pdu3 branch (.1.3.6.1.4.1.232.165.11).

The template uses SNMP discovery to automatically detect and monitor all phases, outlets, breaker groups, and connected environmental probes, enabling capacity planning and early detection of electrical or environmental anomalies in data center power infrastructure.

## Requirements

- Zabbix version: 7.4 and higher
- SNMPv2c enabled on the PDU
- Network connectivity between Zabbix server/proxy and PDU management interface

## Tested versions

This template has been tested on:
- HPE Managed PDU model P9S18A (230V, 32A, 7.4kVA, 50/60Hz, 24 outlets, 3 sections)
- Zabbix 7.4

## Configuration

> Zabbix should be configured according to the instructions in the [Templates out of the box](https://www.zabbix.com/documentation/current/manual/config/templates_out_of_the_box) section.

## Setup

1. Configure the SNMP community string on the HPE PDU via its web management interface

2. Verify SNMP connectivity from the Zabbix server/proxy:
   ```
   snmpwalk -v2c -c <community> <pdu-ip> .1.3.6.1.4.1.232.165.11
   ```

3. Create a new host in Zabbix for the PDU with an SNMP interface pointing to the PDU management IP

4. Set the host macro `{$SNMP_COMMUNITY}` to match the community string configured on the PDU

5. Link this template to the host

6. Adjust threshold macros as needed for your environment (voltage, current, temperature, humidity)

### Macros used

|Name|Description|Default|
|----|-----------|-------|
|{$SNMP_COMMUNITY}|SNMP community string|public|
|{$SNMP.TIMEOUT}|SNMP availability check timeout|5m|
|{$UPDATEINT}|Default polling interval|60s|
|{$UPDATEINT.SLOW}|Slow polling interval for energy counters|300s|
|{$HISTORY}|Item history retention period|90d|
|{$TRENDS}|Item trends retention period|365d|
|{$INPUT.CURRENT.WARN}|Input current warning threshold (80% of 32A)|25.6|
|{$INPUT.CURRENT.CRIT}|Input current critical threshold (90% of 32A)|28.8|
|{$VOLTAGE.HIGH.WARN}|Input voltage high warning (EU 230V +10%)|253|
|{$VOLTAGE.LOW.WARN}|Input voltage low warning (EU 230V -10%)|207|
|{$SECTION.LOAD.WARN}|Section percent load warning threshold|80|
|{$SECTION.LOAD.CRIT}|Section percent load critical threshold|90|
|{$OUTLET.LOAD.WARN}|Outlet percent load warning threshold|80|
|{$OUTLET.LOAD.CRIT}|Outlet percent load critical threshold|90|
|{$TEMP.HIGH.WARN}|Temperature high warning (C)|35|
|{$TEMP.HIGH.CRIT}|Temperature high critical (C)|40|
|{$HUMID.HIGH.WARN}|Humidity high warning (%RH)|60|
|{$HUMID.HIGH.CRIT}|Humidity high critical (%RH)|70|
|{$HUMID.LOW.WARN}|Humidity low warning (%RH)|30|
|{$HUMID.LOW.CRIT}|Humidity low critical (%RH)|20|

### Items

|Name|Description|Type|Key and additional info|
|----|-----------|----|-----------------------|
|PDU: Manufacturer|PDU manufacturer name|SNMP agent|hpePdu.manufacturer|
|PDU: Model|PDU model identifier|SNMP agent|hpePdu.model|
|PDU: Firmware version|PDU firmware version|SNMP agent|hpePdu.firmware|
|PDU: Part number|PDU part/product number|SNMP agent|hpePdu.partNumber|
|PDU: Serial number|PDU serial number|SNMP agent|hpePdu.serial|
|PDU: MAC address|PDU network MAC address|SNMP agent|hpePdu.mac|
|PDU: Device rating|PDU electrical rating string|SNMP agent|hpePdu.rating|
|PDU: Device name|User-configured PDU device name|SNMP agent|hpePdu.deviceName|
|PDU: Outlet count|Total number of outlets on the PDU|SNMP agent|hpePdu.outletCount|
|System description|MIB-II system description|SNMP agent|system.descr|
|System name|MIB-II system name|SNMP agent|system.name|
|System uptime|Device uptime (sysUpTime)|SNMP agent|system.uptime|
|System contact|MIB-II system contact|SNMP agent|system.contact|
|System location|MIB-II system location|SNMP agent|system.location|
|SNMP traps (fallback)|Catch-all for SNMP traps not handled elsewhere|SNMP trap|snmptrap.fallback|
|SNMP agent availability|Zabbix internal SNMP agent availability check|Internal|zabbix[host,snmp,available]|
|Input: Frequency|Input line frequency (Hz)|SNMP agent|hpePdu.input.frequency|
|Input: Frequency status|Input frequency status (Good / Out of Range)|SNMP agent|hpePdu.input.frequencyStatus|
|Input: Apparent power (VA)|Total input apparent power|SNMP agent|hpePdu.input.powerVA|
|Input: Active power (W)|Total input active power|SNMP agent|hpePdu.input.powerWatts|
|Input: Total energy (kWh)|Cumulative input energy consumption|SNMP agent|hpePdu.input.energy|
|Input: Power factor|Input power factor|SNMP agent|hpePdu.input.powerFactor|
|Input: Reactive power (VAR)|Total input reactive power|SNMP agent|hpePdu.input.powerVAR|

### Discovery rules

|Name|Description|Type|Key and additional info|
|----|-----------|----|-----------------------|
|Input phase discovery|Discovers input phases/sections with non-zero current rating|SNMP agent|hpePdu.inputPhase.discovery|
|Outlet discovery|Discovers individual outlets on the PDU|SNMP agent|hpePdu.outlet.discovery|
|Breaker group discovery|Discovers breaker groups with active breaker status|SNMP agent|hpePdu.group.discovery|
|Temperature sensor discovery|Discovers connected temperature probes|SNMP agent|hpePdu.temperature.discovery|
|Humidity sensor discovery|Discovers connected humidity probes|SNMP agent|hpePdu.humidity.discovery|
|Contact sensor discovery|Discovers connected contact/door sensors|SNMP agent|hpePdu.contact.discovery|

### Item prototypes for Input phase discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|-----------------------|
|Phase {#PHASE_INDEX}: Voltage|Phase voltage in V (0.1V scaling)|SNMP agent|hpePdu.phase.voltage[{#SNMPINDEX}]|
|Phase {#PHASE_INDEX}: Current rating|Phase rated current capacity in A (0.01A scaling)|SNMP agent|hpePdu.phase.currentRating[{#SNMPINDEX}]|
|Phase {#PHASE_INDEX}: Current|Phase current draw in A (0.01A scaling)|SNMP agent|hpePdu.phase.current[{#SNMPINDEX}]|
|Phase {#PHASE_INDEX}: Percent load|Phase load percentage (0.1% scaling)|SNMP agent|hpePdu.phase.percentLoad[{#SNMPINDEX}]|
|Phase {#PHASE_INDEX}: Apparent power (VA)|Phase apparent power|SNMP agent|hpePdu.phase.powerVA[{#SNMPINDEX}]|
|Phase {#PHASE_INDEX}: Active power (W)|Phase active power|SNMP agent|hpePdu.phase.powerWatts[{#SNMPINDEX}]|
|Phase {#PHASE_INDEX}: Energy (kWh)|Cumulative phase energy consumption|SNMP agent|hpePdu.phase.energy[{#SNMPINDEX}]|
|Phase {#PHASE_INDEX}: Power factor|Phase power factor (0.01 scaling)|SNMP agent|hpePdu.phase.powerFactor[{#SNMPINDEX}]|

### Trigger prototypes for Input phase discovery

|Name|Description|Expression|Severity|Dependencies and additional info|
|----|-----------|----------|--------|--------------------------------|
|Phase {#PHASE_INDEX}: Voltage too high|Phase voltage exceeds high warning threshold|last(/HPE Managed PDU by SNMP/hpePdu.phase.voltage[{#SNMPINDEX}])>{$VOLTAGE.HIGH.WARN}|Warning||
|Phase {#PHASE_INDEX}: Voltage too low|Phase voltage is below low warning threshold|last(/HPE Managed PDU by SNMP/hpePdu.phase.voltage[{#SNMPINDEX}])<{$VOLTAGE.LOW.WARN}|Warning||
|Phase {#PHASE_INDEX}: Current above {$INPUT.CURRENT.WARN}A|Phase current exceeds warning threshold|last(/HPE Managed PDU by SNMP/hpePdu.phase.current[{#SNMPINDEX}])>{$INPUT.CURRENT.WARN}|Warning||
|Phase {#PHASE_INDEX}: Current above {$INPUT.CURRENT.CRIT}A|Phase current exceeds critical threshold|last(/HPE Managed PDU by SNMP/hpePdu.phase.current[{#SNMPINDEX}])>{$INPUT.CURRENT.CRIT}|High|**Depends on**: Phase {#PHASE_INDEX}: Current above {$INPUT.CURRENT.WARN}A|
|Phase {#PHASE_INDEX}: Load above {$SECTION.LOAD.WARN}%|Section/phase load exceeds warning threshold|last(/HPE Managed PDU by SNMP/hpePdu.phase.percentLoad[{#SNMPINDEX}])>{$SECTION.LOAD.WARN}|Warning||
|Phase {#PHASE_INDEX}: Load above {$SECTION.LOAD.CRIT}%|Section/phase load exceeds critical threshold|last(/HPE Managed PDU by SNMP/hpePdu.phase.percentLoad[{#SNMPINDEX}])>{$SECTION.LOAD.CRIT}|High|**Depends on**: Phase {#PHASE_INDEX}: Load above {$SECTION.LOAD.WARN}%|

### Item prototypes for Outlet discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|-----------------------|
|Outlet {#OUTLET_NAME}: Name|Outlet user-assigned name|SNMP agent|hpePdu.outlet.name[{#OUTLET_INDEX}]|
|Outlet {#OUTLET_NAME}: Type|Outlet connector type (IEC C13, IEC C19, UK, French, Schuko)|SNMP agent|hpePdu.outlet.type[{#OUTLET_INDEX}]|
|Outlet {#OUTLET_NAME}: Current rating|Outlet rated current capacity in A (0.01A scaling)|SNMP agent|hpePdu.outlet.currentRating[{#OUTLET_INDEX}]|
|Outlet {#OUTLET_NAME}: Current|Outlet current draw in A (0.01A scaling)|SNMP agent|hpePdu.outlet.current[{#OUTLET_INDEX}]|
|Outlet {#OUTLET_NAME}: Percent load|Outlet load percentage (0.1% scaling)|SNMP agent|hpePdu.outlet.percentLoad[{#OUTLET_INDEX}]|
|Outlet {#OUTLET_NAME}: Apparent power (VA)|Outlet apparent power|SNMP agent|hpePdu.outlet.powerVA[{#OUTLET_INDEX}]|
|Outlet {#OUTLET_NAME}: Active power (W)|Outlet active power|SNMP agent|hpePdu.outlet.powerWatts[{#OUTLET_INDEX}]|
|Outlet {#OUTLET_NAME}: Energy (kWh)|Cumulative outlet energy consumption|SNMP agent|hpePdu.outlet.energy[{#OUTLET_INDEX}]|
|Outlet {#OUTLET_NAME}: Power factor|Outlet power factor (0.01 scaling)|SNMP agent|hpePdu.outlet.powerFactor[{#OUTLET_INDEX}]|

### Trigger prototypes for Outlet discovery

|Name|Description|Expression|Severity|Dependencies and additional info|
|----|-----------|----------|--------|--------------------------------|
|Outlet {#OUTLET_NAME}: Load above {$OUTLET.LOAD.WARN}%|Outlet load exceeds warning threshold|last(/HPE Managed PDU by SNMP/hpePdu.outlet.percentLoad[{#OUTLET_INDEX}])>{$OUTLET.LOAD.WARN}|Warning||
|Outlet {#OUTLET_NAME}: Load above {$OUTLET.LOAD.CRIT}%|Outlet load exceeds critical threshold|last(/HPE Managed PDU by SNMP/hpePdu.outlet.percentLoad[{#OUTLET_INDEX}])>{$OUTLET.LOAD.CRIT}|High|**Depends on**: Outlet {#OUTLET_NAME}: Load above {$OUTLET.LOAD.WARN}%|

### Item prototypes for Breaker group discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|-----------------------|
|Group {#GROUP_NAME}: Voltage|Breaker group voltage in V (0.1V scaling)|SNMP agent|hpePdu.group.voltage[{#GROUP_INDEX}]|
|Group {#GROUP_NAME}: Current rating|Breaker group rated current capacity in A (0.01A scaling)|SNMP agent|hpePdu.group.currentRating[{#GROUP_INDEX}]|
|Group {#GROUP_NAME}: Current|Breaker group current draw in A (0.01A scaling)|SNMP agent|hpePdu.group.current[{#GROUP_INDEX}]|
|Group {#GROUP_NAME}: Percent load|Breaker group load percentage (0.1% scaling)|SNMP agent|hpePdu.group.percentLoad[{#GROUP_INDEX}]|
|Group {#GROUP_NAME}: Apparent power (VA)|Breaker group apparent power|SNMP agent|hpePdu.group.powerVA[{#GROUP_INDEX}]|
|Group {#GROUP_NAME}: Active power (W)|Breaker group active power|SNMP agent|hpePdu.group.powerWatts[{#GROUP_INDEX}]|
|Group {#GROUP_NAME}: Power factor|Breaker group power factor (0.01 scaling)|SNMP agent|hpePdu.group.powerFactor[{#GROUP_INDEX}]|
|Group {#GROUP_NAME}: Breaker status|Breaker status (Not Applicable / On / Off)|SNMP agent|hpePdu.group.breakerStatus[{#GROUP_INDEX}]|

### Trigger prototypes for Breaker group discovery

|Name|Description|Expression|Severity|Dependencies and additional info|
|----|-----------|----------|--------|--------------------------------|
|Group {#GROUP_NAME}: Breaker tripped|Breaker for group has tripped or been turned off|last(/HPE Managed PDU by SNMP/hpePdu.group.breakerStatus[{#GROUP_INDEX}])=3|High||

### Item prototypes for Temperature sensor discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|-----------------------|
|Temperature {#TEMP_NAME} [{#TEMP_INDEX}]: Value|Temperature reading in degrees Celsius|SNMP agent|hpePdu.temp.value[{#SNMPINDEX}]|
|Temperature {#TEMP_NAME} [{#TEMP_INDEX}]: Probe status|Temperature probe connection status (Disconnected / Connected / Bad)|SNMP agent|hpePdu.temp.probeStatus[{#SNMPINDEX}]|

### Trigger prototypes for Temperature sensor discovery

|Name|Description|Expression|Severity|Dependencies and additional info|
|----|-----------|----------|--------|--------------------------------|
|Temperature {#TEMP_NAME} [{#TEMP_INDEX}]: Above {$TEMP.HIGH.WARN}C|Temperature exceeds warning threshold|last(/HPE Managed PDU by SNMP/hpePdu.temp.value[{#SNMPINDEX}])>{$TEMP.HIGH.WARN}|Warning||
|Temperature {#TEMP_NAME} [{#TEMP_INDEX}]: Above {$TEMP.HIGH.CRIT}C|Temperature exceeds critical threshold|last(/HPE Managed PDU by SNMP/hpePdu.temp.value[{#SNMPINDEX}])>{$TEMP.HIGH.CRIT}|High|**Depends on**: Temperature {#TEMP_NAME} [{#TEMP_INDEX}]: Above {$TEMP.HIGH.WARN}C|
|Temperature {#TEMP_NAME} [{#TEMP_INDEX}]: Probe disconnected|Temperature probe has been disconnected|last(/HPE Managed PDU by SNMP/hpePdu.temp.probeStatus[{#SNMPINDEX}])<>2|Warning||

### Item prototypes for Humidity sensor discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|-----------------------|
|Humidity {#HUMID_NAME} [{#HUMID_INDEX}]: Value|Humidity reading in percent relative humidity|SNMP agent|hpePdu.humid.value[{#HUMID_INDEX}]|
|Humidity {#HUMID_NAME} [{#HUMID_INDEX}]: Probe status|Humidity probe connection status (Disconnected / Connected / Bad)|SNMP agent|hpePdu.humid.probeStatus[{#HUMID_INDEX}]|

### Trigger prototypes for Humidity sensor discovery

|Name|Description|Expression|Severity|Dependencies and additional info|
|----|-----------|----------|--------|--------------------------------|
|Humidity {#HUMID_NAME} [{#HUMID_INDEX}]: Above {$HUMID.HIGH.WARN}%RH|Humidity exceeds high warning threshold|last(/HPE Managed PDU by SNMP/hpePdu.humid.value[{#HUMID_INDEX}])>{$HUMID.HIGH.WARN}|Warning||
|Humidity {#HUMID_NAME} [{#HUMID_INDEX}]: Above {$HUMID.HIGH.CRIT}%RH|Humidity exceeds high critical threshold|last(/HPE Managed PDU by SNMP/hpePdu.humid.value[{#HUMID_INDEX}])>{$HUMID.HIGH.CRIT}|High|**Depends on**: Humidity {#HUMID_NAME} [{#HUMID_INDEX}]: Above {$HUMID.HIGH.WARN}%RH|
|Humidity {#HUMID_NAME} [{#HUMID_INDEX}]: Below {$HUMID.LOW.WARN}%RH|Humidity is below low warning threshold|last(/HPE Managed PDU by SNMP/hpePdu.humid.value[{#HUMID_INDEX}])<{$HUMID.LOW.WARN}|Warning||
|Humidity {#HUMID_NAME} [{#HUMID_INDEX}]: Below {$HUMID.LOW.CRIT}%RH|Humidity is below low critical threshold|last(/HPE Managed PDU by SNMP/hpePdu.humid.value[{#HUMID_INDEX}])<{$HUMID.LOW.CRIT}|High|**Depends on**: Humidity {#HUMID_NAME} [{#HUMID_INDEX}]: Below {$HUMID.LOW.WARN}%RH|
|Humidity {#HUMID_NAME} [{#HUMID_INDEX}]: Probe disconnected|Humidity probe has been disconnected|last(/HPE Managed PDU by SNMP/hpePdu.humid.probeStatus[{#HUMID_INDEX}])<>2|Warning||

### Item prototypes for Contact sensor discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|-----------------------|
|Contact {#CONTACT_NAME} [{#CONTACT_INDEX}]: State|Contact/door sensor state (Open / Closed)|SNMP agent|hpePdu.contact.state[{#CONTACT_INDEX}]|
|Contact {#CONTACT_NAME} [{#CONTACT_INDEX}]: Probe status|Contact sensor connection status (Disconnected / Connected / Bad)|SNMP agent|hpePdu.contact.probeStatus[{#CONTACT_INDEX}]|

### Trigger prototypes for Contact sensor discovery

|Name|Description|Expression|Severity|Dependencies and additional info|
|----|-----------|----------|--------|--------------------------------|
|Contact {#CONTACT_NAME} [{#CONTACT_INDEX}]: Door/contact opened|Door switch or contact sensor has been opened|last(/HPE Managed PDU by SNMP/hpePdu.contact.state[{#CONTACT_INDEX}])=1|Warning||

### Triggers

|Name|Description|Expression|Severity|Dependencies and additional info|
|----|-----------|----------|--------|--------------------------------|
|Firmware version has changed|PDU firmware version has changed. Verify the update was intentional.|change(/HPE Managed PDU by SNMP/hpePdu.firmware)=1 and length(last(/HPE Managed PDU by SNMP/hpePdu.firmware))>0|Info||
|No SNMP data collection|SNMP is not available for polling. Check SNMP community, device connectivity, and firewall rules.|max(/HPE Managed PDU by SNMP/zabbix[host,snmp,available],{$SNMP.TIMEOUT})=0|Warning||
|Device has been restarted|Device uptime is less than 10 minutes, indicating a recent restart.|last(/HPE Managed PDU by SNMP/system.uptime)<600|Info||
|Input frequency out of range|Input line frequency is outside acceptable range.|last(/HPE Managed PDU by SNMP/hpePdu.input.frequencyStatus)<>1|High||
