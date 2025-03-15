# ESXI SMART Health

## Overview

This template is designed for the effortless monitoring of HDD storage devices in VMware ESXi environments using S.M.A.R.T. technology. It enables early detection of disk failures and performance issues without requiring additional scripts beyond the built-in SSH functionality.

The template collects and monitors critical S.M.A.R.T. attributes that can indicate potential disk problems, including:
- Current Pending Sectors
- Reallocated Sector Count
- Offline Uncorrectable Sectors
- Power On Hours

## Requirements

- Zabbix version: 7.2 and higher
- VMware ESXi host with SSH enabled
- smartmontools installed on ESXi host
- SSH Public Key authentication configured

## Tested versions

This template has been tested on:
- VMware ESXi 7.0
- Zabbix 7.2

## Configuration

> Zabbix should be configured according to the instructions in the [Templates out of the box](https://www.zabbix.com/documentation/current/manual/config/templates_out_of_the_box) section.

## Setup

1. Enable SSH on your ESXi

2. Install smartmontools to the server

[VIB(vSphere Installation Bundle) with smartctl for esxi 7/8](https://github.com/bsv9/smartctl-esxi-vib)

3. Put your SSH key to the esxi server

```
curl -k -X PUT  --data "@$_PATH_TO_KEY"  -u root:<pw>  https://<ip>/host/ssh_root_authorized_keys
```

> [!WARNING]
> Be careful, on ESXI 8.0 U3 this will return a 404.
> You should log in to the server and update the key manually in  /etc/ssh/keys-root/authorized_keys

4. Add SSH Key to Zabbix server

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
|Get HDD Devices|Discovers all HDD devices on the ESXi host and extracts their serial numbers|SSH|ssh.run[smart]|

### Discovery rules

|Name|Description|Type|Key and additional info|
|----|-----------|----|-----------------------|
|SMART Discovery|Discovers all storage devices on the ESXi host|Dependent item|smart.discovery|

### Item prototypes for SMART Discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|-----------------------|
|SMART {#DISK_SERIAL} Current Pending Sector|Number of unstable sectors waiting to be remapped|Dependent item|smart.status[{#DISK_ID},Current_Pending_Sector]|
|SMART {#DISK_SERIAL} Offline Uncorrectable|Count of uncorrectable errors detected during offline data collection|Dependent item|smart.status[{#DISK_ID},Offline_Uncorrectable]|
|SMART {#DISK_SERIAL} Power On Hours|Total number of hours the device has been powered on|Dependent item|smart.status[{#DISK_ID},Power_On_Hours]|
|SMART {#DISK_SERIAL} Reallocated Sector Count|Count of remapped sectors|Dependent item|smart.status[{#DISK_ID},Reallocated_Sector_Ct]|
|SMART Info {#DISK_SERIAL}|Master item containing all S.M.A.R.T. information for a specific disk|SSH|ssh.run[{#DISK_ID}]|

### Triggers

|Name|Description|Expression|Severity|Dependencies and additional info|
|----|-----------|----------|--------|--------------------------------|
|SMART {#NAME}: Current Pending Sector|Triggers when current pending sectors are found, indicating potential bad blocks|last(/ESXI SMART Health/smart.status[{#DISK_ID},Current_Pending_Sector])>0|Average||
