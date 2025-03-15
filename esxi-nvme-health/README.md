# ESXI NVME Health

## Overview

The ESXI NVME Health template is designed for comprehensive monitoring of NVMe storage devices in VMware ESXi environments. This template provides detailed insights into the health, performance, and lifecycle metrics of NVMe drives, enabling proactive maintenance and early detection of potential failures.
This template is designed for the effortless monitoring of NVME devices in VMware ESXi environments. It enables early detection of Media Failure failures and performance issues without requiring additional scripts beyond the built-in SSH functionality.

## Key Metrics Collected

The template collects the following key NVMe metrics:

| Metric | Description | Importance |
|--------|-------------|------------|
| AvailableSpare | Percentage of spare capacity available | Critical for endurance monitoring |
| CompositeTemperature | Overall temperature of the NVMe device | Essential for thermal management |
| ControllerBusyTime | Time the controller has been busy processing commands | Performance indicator |
| MediaErrors | Count of data integrity errors detected | Critical failure indicator |
| PercentageUsed | Estimated percentage of rated write endurance used | Lifecycle planning |
| PowerCycles | Number of power on/off cycles | Wear indicator |
| PowerOnHours | Total hours the device has been powered on | Lifecycle tracking |

## Requirements

- Zabbix version: 7.2 and higher
- VMware ESXi host with SSH enabled
- SSH Public Key authentication configured

## Tested versions

This template has been tested on:
- VMware ESXi 7.0
- Zabbix 7.2

## Configuration

> Zabbix should be configured according to the instructions in the [Templates out of the box](https://www.zabbix.com/documentation/current/manual/config/templates_out_of_the_box) section.

## Setup

1. Enable SSH on your ESXi

2. Put your SSH key to the ESXI server

```
curl -k -X PUT  --data "@$PATH_TO_YOU_SSH_KEY"  -u root:<pw>  https://<ip>/host/ssh_root_authorized_keys
```

> [!WARNING]
> Be careful, on ESXI 8.0 U3 this will return a 404.
> You should log in to the server and update the key manually in  /etc/ssh/keys-root/authorized_keys

3. Add SSH Key to Zabbix server

Put you ssh key and private key to the zabbix server folder
/var/lib/zabbix/ssh_keys/$KEY
/var/lib/zabbix/ssh_keys/$KEY.pub

4. Create a new host in Zabbix for the ESXi server

5. Set the host macros required for SSH authentication:
   ```text
   {$ESXI.SSH.KEY}
   ```

6. Link the template to the host

### Macros used

|Name|Description|Default|
|----|-----------|-------|
|{$ESXI.SSH.KEY}|SSH key name used for authentication with the ESXi host|esxi|

### Items

|Name|Description|Type|Key and additional info|
|----|-----------|----|-----------------------|
|Get NVME Devices|Discovers all NVMe devices on the ESXi host|SSH|ssh.run[nvme]|

### Discovery rules

|Name|Description|Type|Key and additional info|
|----|-----------|----|-----------------------|
|NVME Discovery|Discovers all NVMe storage devices on the ESXi host|Dependent item|nvme.discovery|

### Item prototypes for NVME Discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|-----------------------|
|NVME {#HBA} AvailableSpare|Percentage of spare capacity available for use|Dependent item|nvme.health[{#HBA},AvailableSpare]|
|NVME {#HBA} CompositeTemperature|Overall temperature of the NVMe device in Celsius|Dependent item|nvme.health[{#HBA},CompositeTemperature]|
|NVME {#HBA} ControllerBusyTime|Time the controller has been busy processing commands (minutes)|Dependent item|nvme.health[{#HBA},ControllerBusyTime]|
|NVME {#HBA} MediaErrors|Count of times where data could not be recovered|Dependent item|nvme.health[{#HBA},MediaErrors]|
|NVME {#HBA} PercentageUsed|Estimated percentage of rated write endurance consumed|Dependent item|nvme.health[{#HBA},PercentageUsed]|
|NVME {#HBA} PowerCycles|Number of power on/off cycles over the life of the device|Dependent item|nvme.health[{#HBA},PowerCycles]|
|NVME {#HBA} Power On Hours|Total hours the device has been powered on|Dependent item|nvme.health[{#HBA},PowerOnHours]|
|NVME Info {#HBA}|Master item containing all NVMe SMART information for a specific device|SSH|ssh.run[{#HBA}]|



------------

# ESXI NVME Health

## Overview

This template is designed for the monitoring of NVMe storage devices in VMware ESXi environments. It leverages ESXi's built-in NVMe management capabilities to collect comprehensive health and performance metrics without requiring external scripts beyond the native SSH functionality.

The template automatically discovers all NVMe devices connected to the ESXi host and monitors critical health parameters including temperature, available spare capacity, endurance metrics, error counts, and performance indicators.

## Requirements

- Zabbix version: 7.2 and higher
- VMware ESXi 6.7 or higher with NVMe CLI support
- SSH enabled on ESXi host
- SSH Public Key authentication configured

## Tested versions

This template has been tested on:
- VMware ESXi 7.0
- Zabbix 7.2

## Configuration

> Zabbix should be configured according to the instructions in the [Templates out of the box](https://www.zabbix.com/documentation/current/manual/config/templates_out_of_the_box) section.

## Setup

1. Enable SSH on your ESXi host through the vSphere client or using the following command:
   ```
   vim-cmd hostsvc/enable_ssh
   ```

2. Configure SSH Public Key authentication between Zabbix server and ESXi host

3. Create a new host in Zabbix for the ESXi server

4. Set the host macro required for SSH authentication:
   ```text
   {$ESXI.SSH.KEY}
   ```

5. Link the template to the host

### Macros used

|Name|Description|Default|
|----|-----------|-------|
|{$ESXI.SSH.KEY}|SSH key name used for authentication with the ESXi host|esxi|

### Items

|Name|Description|Type|Key and additional info|
|----|-----------|----|-----------------------|
|Get NVME Devices|Discovers all NVMe devices on the ESXi host|SSH|ssh.run[nvme]|

### Discovery rules

|Name|Description|Type|Key and additional info|
|----|-----------|----|-----------------------|
|NVME Discovery|Discovers all NVMe storage devices on the ESXi host|Dependent item|nvme.discovery|

### Item prototypes for NVME Discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|-----------------------|
|NVME {#HBA} AvailableSpare|Percentage of spare capacity available for use|Dependent item|nvme.health[{#HBA},AvailableSpare]|
|NVME {#HBA} CompositeTemperature|Overall temperature of the NVMe device in Celsius|Dependent item|nvme.health[{#HBA},CompositeTemperature]|
|NVME {#HBA} ControllerBusyTime|Time the controller has been busy processing commands (minutes)|Dependent item|nvme.health[{#HBA},ControllerBusyTime]|
|NVME {#HBA} MediaErrors|Count of times where data could not be recovered|Dependent item|nvme.health[{#HBA},MediaErrors]|
|NVME {#HBA} PercentageUsed|Estimated percentage of rated write endurance consumed|Dependent item|nvme.health[{#HBA},PercentageUsed]|
|NVME {#HBA} PowerCycles|Number of power on/off cycles over the life of the device|Dependent item|nvme.health[{#HBA},PowerCycles]|
|NVME {#HBA} Power On Hours|Total hours the device has been powered on|Dependent item|nvme.health[{#HBA},PowerOnHours]|
|NVME Info {#HBA}|Master item containing all NVMe SMART information for a specific device|SSH|ssh.run[{#HBA}]|


### Triggers

|Name|Description|Expression|Severity|Dependencies and additional info|
|----|-----------|----------|--------|--------------------------------|
|NVME {#HBA}: Media errors detected|Triggers when media errors are detected, indicating potential data integrity issues|last(/ESXI NVME Health/nvme.health[{#HBA},MediaErrors])>0|High||
|NVME {#HBA}: Temperature warning|Triggers when NVMe device temperature exceeds 60°C|last(/ESXI NVME Health/nvme.health[{#HBA},CompositeTemperature])>60|Warning||
|NVME {#HBA}: Critical temperature|Triggers when NVMe device temperature exceeds 70°C|last(/ESXI NVME Health/nvme.health[{#HBA},CompositeTemperature])>70|High|**Depends on**:<br><ul><li>NVME {#HBA}: Temperature warning</li></ul>|
|NVME {#HBA}: Low spare capacity|Triggers when available spare capacity drops below 20%|last(/ESXI NVME Health/nvme.health[{#HBA},AvailableSpare])<20|Warning||
|NVME {#HBA}: Critical spare capacity|Triggers when available spare capacity drops below 10%|last(/ESXI NVME Health/nvme.health[{#HBA},AvailableSpare])<10|High|**Depends on**:<br><ul><li>NVME {#HBA}: Low spare capacity</li></ul>|
|NVME {#HBA}: High endurance usage|Triggers when percentage of rated write endurance used exceeds 80%|last(/ESXI NVME Health/nvme.health[{#HBA},PercentageUsed])>80|Warning||
|NVME {#HBA}: Critical endurance usage|Triggers when percentage of rated write endurance used exceeds 95%|last(/ESXI NVME Health/nvme.health[{#HBA},PercentageUsed])>95|High|**Depends on**:<br><ul><li>NVME {#HBA}: High endurance usage</li></ul>|
