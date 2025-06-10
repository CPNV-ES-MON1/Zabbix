Mon 1 - Zabbix documentation <br>
ICT - GGR - RDI

# Windows client configuration

## Table of content
- [Windows client configuration](#windows-client-configuration)
  - [Table of content](#table-of-content)
- [1. Installation of the zabbix agent](#1-installation-of-the-zabbix-agent)
- [2. configuration on the client](#2-configuration-on-the-client)
- [3. configuration on zabbix server](#3-configuration-on-zabbix-server)
  - [3.1. adding the host](#31-adding-the-host)
  - [3.2. item configuration](#32-item-configuration)
  - [3.3. add the host to the dashboard with the item](#33-add-the-host-to-the-dashboard-with-the-item)
- [4. script creation on the windows client](#4-script-creation-on-the-windows-client)
  - [4.1. zabbix agent configuration](#41-zabbix-agent-configuration)
  - [4.2. host item configuration](#42-host-item-configuration)


<br>
<br>

# 1. Installation of the zabbix agent

https://www.zabbix.com/download_agents?version=7.0+LTS&release=7.0.12&os=Windows&os_version=Any&hardware=amd64&encryption=OpenSSL&packaging=MSI&show_legacy=0
- Windows / Any / amd64 / 7.0 LTS / OpenSSL / MSI
- Click on Download Zabbix agent 2 v7.0.12
- Then open the MSI that has been downloaded

# 2. configuration on the client

**installation steps**
```
after accepting the license we let the parameter of the next page by default.
```
**zabbix server configuration**
```
hostname : <MON1-CLI-WIN>
ip : <zabbix server ip>
port : 10050 (by default)
PSK : no
add agent location PATH : yes
```
# 3. configuration on zabbix server

## 3.1. adding the host
```
monitoring>host>create host
```
**Tab host**
```
- hostname : <MON1-CLI-WIN>
- visible name : <MON1-CLI-WIN>
- click on Templates
- search for windows by zabbix agent
- host group : virtual machines
- interfaces:
  - select: agent
  - ip : <windows host ip>
  - port : 10050 (by default)
```

**Tab inventory**
```
use the automatic option
```
you now can press "Add" to save the new host

## 3.2. item configuration
```
Go to Data collection>hosts>

- Click on "Items" of the windows host <MON1-CLI-WIN> (be sure not to have any filters and click Apply)

select "CPU utilization"
- Update interval : 5s
```

## 3.3. add the host to the dashboard with the item
```
Dashboards>Top hosts by CPU utilization>Setting icon
- Hosts: Select>write Virtual machines>select <MON1-CLI-WIN>
- Columns: Add
  - Name: CPU utilization
  - Item name: type "CPU utilisation" and select it
- Apply
```
CLICK ON Save changes

# 4. script creation on the windows client
On the windows machine, go to C:\ and create a folder named "script"
Create 3 file under "C:/script"
```
cpu_last_state.txt
cpu_last_state.txt
cpu_log.bat
```
put the folling script in the .bat file
```
@echo off
setlocal

set "STATE_FILE=C:\script\cpu_last_state.txt"
set "LOG_FILE=C:\Program Files\Zabbix Agent\zabbix_agentd.log"
set "THRESHOLD=90"

for /f "tokens=2 delims==." %%A in ('wmic cpu get loadpercentage /value ^| findstr LoadPercentage') do set /a CURRENT=%%A

if not exist "%STATE_FILE%" (
    echo %CURRENT% > "%STATE_FILE%"
)

set /p PREV=<"%STATE_FILE%"

:: Check if threshold was crossed
if %CURRENT% GEQ %THRESHOLD% (
    if %PREV% LSS %THRESHOLD% (
        echo %DATE% %TIME% CPU usage rose above %THRESHOLD%%: %CURRENT%%% >> "%LOG_FILE%"
    )
) else (
    if %PREV% GEQ %THRESHOLD% (
        echo %DATE% %TIME% CPU usage fell below %THRESHOLD%%: %CURRENT%%% >> "%LOG_FILE%"
    )
)

echo %CURRENT% > "%STATE_FILE%"

:: Always output current value
echo %CURRENT%
```
## 4.1. zabbix agent configuration
add the line in "C:\programes Files\Zabbix Agent\zabbix_agentd.conf"
```
UserParameter=cpu.util.custom, C:\Script\cpu_log.bat
```
## 4.2. host item configuration
add an item under the windows host
```
Type = Zabbix agent
name = CPU trigger
key = cpu.util.custom
Type of information = Numeric
Units = %
Update interval = 5s
```
