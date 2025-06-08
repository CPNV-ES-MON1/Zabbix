Mon 1 - Zabbix documentation <br>
ICT - GGR - RDI

# Client linux configuration

## Table of content
- [Client linux configuration](#client-linux-configuration)
  - [Table of content](#table-of-content)
- [1. Pakages installation](#1-pakages-installation)
- [2. Agent installation](#2-agent-installation)
- [3. Restart and enable services](#3-restart-and-enable-services)
- [4. Agent configuration](#4-agent-configuration)
- [5. Webui configuration](#5-webui-configuration)
  - [5.1. Add host](#51-add-host)
  - [5.2. configuration de l'item](#52-configuration-de-litem)
  - [5.3. ajout de l'host sur ele dashboard avec l'item du CPU](#53-ajout-de-lhost-sur-ele-dashboard-avec-litem-du-cpu)



<br>
<br>

# 1. Pakages installation
Strat by installing the pakages you'll need and update the list of installed pakages

**input**
```
wget https://repo.zabbix.com/zabbix/7.2/release/debian/pool/main/z/zabbix-release/zabbix-release_latest_7.2+debian11_all.deb
```
**output**
```
--2025-06-04 10:46:01--  https://repo.zabbix.com/zabbix/7.2/release/debian/pool/main/z/zabbix-release/zabbix-release_latest_7.2+debian11_all.deb
Resolving repo.zabbix.com (repo.zabbix.com)... 178.128.6.101, 2604:a880:2:d0::2062:d001
Connecting to repo.zabbix.com (repo.zabbix.com)|178.128.6.101|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 7288 (7.1K) [application/octet-stream]
Saving to: ‘zabbix-release_latest_7.2+debian11_all.deb.2’

zabbix-release_latest_7.2+debian11_all.deb.2                100%[========================================================================================================================================>]   7.12K  --.-KB/s    in 0s

2025-06-04 10:46:02 (221 MB/s) - ‘zabbix-release_latest_7.2+debian11_all.deb.2’ saved [7288/7288]
```
<br>
<br>

**input**
```
 sudo dpkg -i zabbix-release_latest_7.2+debian11_all.deb
```
**output**
```
(Reading database ... 141484 files and directories currently installed.)
Preparing to unpack zabbix-release_latest_7.2+debian11_all.deb ...
Unpacking zabbix-release (1:7.2-1+debian11) over (1:7.2-1+debian11) ...
Setting up zabbix-release (1:7.2-1+debian11) ...
```
<br>
<br>

**input**
```
sudo apt update
```
**output**
```
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
All packages are up to date.
```
<br>
<br>

# 2. Agent installation
**input**
```
sudo apt install zabbix-agent
```
**output**
```
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
zabbix-agent is already the newest version (1:7.2.7-1+debian11).
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```
<br>
<br>

# 3. Restart and enable services
**input**
```
sudo systemctl restart zabbix-agent
```
**ouput** - no output

<br>

**input**
```
sudo systemctl enable zabbix-agent
```
**ouput**
```
Synchronizing state of zabbix-agent.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable zabbix-agent
```
<br>
<br>

# 4. Agent configuration
Check in `/etc/zabbix/zabbix_agentd.conf` that the following settings are well setup:

```
Server=192.168.153.140
ListenPort=10050
Hostname=Zabbix-client
```
<br>
<br>

# 5. Webui configuration

## 5.1. Add host
In `Monitoring > Hosts` select `create host`

**onglet host**
```
here we are completing the folling paremeter:

Hostname zabbix = [name]
visible name = [name]
chose template= "linux by zabbix agent"
!!WARNING!! do not use the "linux by zabbix agent active"
Host group = "Virtual machine"
add interface with ip and port for the agent (10050 by default)
```
**onglet inventory**
```
here juste chose the option "automiatic" to enter the host to the inventory
```
## 5.2. configuration de l'item
```
undercollection>hosts>items of the host
we chose the CPU utilisazion item and change the following parameter

Update interval=5s
```
## 5.3. ajout de l'host sur ele dashboard avec l'item du CPU
```
under Dashboad>parameter
add the host to the dashboard
add the collumn CPU utilization
```
