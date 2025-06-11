Mon 1 - Zabbix documentation <br>
ICT - GGR - RDI

# Client linux configuration

## Table of content
- [1. Packages installation](#1-packages-installation)
- [2. Agent installation](#2-agent-installation)
- [3. Restart and enable services](#3-restart-and-enable-services)
- [4. Agent configuration in `/etc/zabbix/zabbix_agent2.conf`](#4-agent-configuration-in-etczabbixzabbix_agent2conf)
- [5. Webui configuration](#5-webui-configuration)
  - [5.1. Add host](#51-add-host)
  - [5.2. Item configuration](#52-item-configuration)
  - [5.3. Adding the host to the main dashboard](#53-adding-the-host-to-the-main-dashboard)



<br>
<br>
<br>

# 1. Packages installation
Start by installing the packages you'll need and update the list of installed pakages

**input**
```
wget https://repo.zabbix.com/zabbix/7.0/debian/pool/main/z/zabbix-release/zabbix-release_latest_7.0+debian12_all.deb
```
**output**
```
--2025-06-10 10:37:30--  https://repo.zabbix.com/zabbix/7.0/debian/pool/main/z/zabbix-release/zabbix-release_latest_7.0+debian12_all.deb
Resolving repo.zabbix.com (repo.zabbix.com)... 178.128.6.101, 2604:a880:2:d0::2062:d001
Connecting to repo.zabbix.com (repo.zabbix.com)|178.128.6.101|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 8096 (7.9K) [application/octet-stream]
Saving to: ‘zabbix-release_latest_7.0+debian12_all.deb’

zabbix-release_latest_7.0+debian12_all.deb           100%[=====================================================================================================================>]   7.91K  --.-KB/s    in 0s      

2025-06-10 10:37:30 (56.4 MB/s) - ‘zabbix-release_latest_7.0+debian12_all.deb’ saved [8096/8096]
```
<br>
<br>

**input**
```
sudo dpkg -i zabbix-release_latest_7.0+debian12_all.deb
```
**output**
```
Selecting previously unselected package zabbix-release.
(Reading database ... 156489 files and directories currently installed.)
Preparing to unpack zabbix-release_latest_7.0+debian12_all.deb ...
Unpacking zabbix-release (1:7.0-2+debian12) ...
Setting up zabbix-release (1:7.0-2+debian12) ...
```
<br>
<br>

**input**
```
sudo apt update
```
**output**
```
Hit:1 http://mirror.init7.net/debian bookworm InRelease
Hit:2 http://security.debian.org/debian-security bookworm-security InRelease
Hit:3 http://mirror.init7.net/debian bookworm-updates InRelease        
Get:4 https://repo.zabbix.com/zabbix-tools/debian-ubuntu bookworm InRelease [2,476 B]
Get:5 https://repo.zabbix.com/zabbix/7.0/debian bookworm InRelease [3,945 B]
Get:6 https://repo.zabbix.com/zabbix-tools/debian-ubuntu bookworm/main Sources [1,166 B]
Get:7 https://repo.zabbix.com/zabbix-tools/debian-ubuntu bookworm/main all Packages [766 B]
Get:8 https://repo.zabbix.com/zabbix/7.0/debian bookworm/main Sources [22.5 kB]
Get:9 https://repo.zabbix.com/zabbix/7.0/debian bookworm/main amd64 Packages [43.9 kB]
Get:10 https://repo.zabbix.com/zabbix/7.0/debian bookworm/main all Packages [8,803 B]
Fetched 83.6 kB in 2s (50.1 kB/s)
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
sudo apt install zabbix-agent2
```
**output**
```
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following package was automatically installed and is no longer required:
  linux-image-6.1.0-32-amd64
Use 'sudo apt autoremove' to remove it.
The following NEW packages will be installed:
  zabbix-agent2
0 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
Need to get 6,015 kB of archives.
After this operation, 19.2 MB of additional disk space will be used.
Get:1 https://repo.zabbix.com/zabbix/7.0/debian bookworm/main amd64 zabbix-agent2 amd64 1:7.0.13-1+debian12 [6,015 kB]
Fetched 6,015 kB in 3s (1,940 kB/s)              
Selecting previously unselected package zabbix-agent2.
(Reading database ... 156498 files and directories currently installed.)
Preparing to unpack .../zabbix-agent2_1%3a7.0.13-1+debian12_amd64.deb ...
Unpacking zabbix-agent2 (1:7.0.13-1+debian12) ...
Setting up zabbix-agent2 (1:7.0.13-1+debian12) ...
Created symlink /etc/systemd/system/multi-user.target.wants/zabbix-agent2.service → /lib/systemd/system/zabbix-agent2.service.
Processing triggers for man-db (2.11.2-2) ...
```
<br>
<br>

# 3. Restart and enable services
**input**
```
sudo systemctl restart zabbix-agent2
```
**ouput** - no output

<br>

**input**
```
sudo systemctl enable zabbix-agent2
```
**ouput**
```
Synchronizing state of zabbix-agent2.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable zabbix-agent2
```
<br>
<br>

# 4. Agent configuration in `/etc/zabbix/zabbix_agent2.conf`

Change value: Server/Hostname
```
Server=<ip of the zabbix server>
Hostname=<zabbix-lin-cli>
ServerActive=<ip of the zabbix server>
```
<br>
<br>

# 5. Webui configuration

## 5.1. Add host

In Monitoring>Hosts>select Create host

**Tab Host**
```
- Host name: <zabbix-lin-cli>
- Visible name: <zabbix-lin-cli>
- click on Templates
  - search for linux by zabbix agent (!!WARNING!! do not use the "linux by zabbix agent active")
- Host groups: Virtual machines
- Interfaces: Add
  - select agent
  - ip: <linux host ip>
  - port: 10050 (by default)
```
**Tab inventory**
```
use the automatic option
```
You now can press "Add" to save your configuration
## 5.2. Item configuration
```
Data collection>hosts>
- Click on "Items" of the linux host <zabbix-lin-cli> (be sure not to have any filters and click Apply)

select "CPU idle time"
- Update interval : 5s
```
## 5.3. Adding the host to the main dashboard
```
Dashboards>Top hosts by CPU utilization>Setting icon
- Hosts: Select>write Virtual machines>select <zabbix-lin-cli>
- Columns: Add
  - Name: CPU utilization
  - Item name: type "CPU utilisation" and select it
- Apply
```
CLICK ON Save changes !!!!!

# Test stress cpu
```
stress --cpu 2 --timeout 300s
```
