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

Open Powershell in admin (ctrl+x / a)

curl -o "$env:USERPROFILE\Downloads\zabbix-agent.msi" "https://cdn.zabbix.com/zabbix/binaries/stable/7.0/7.0.12/zabbix_agent-7.0.12-windows-amd64-openssl.msi"

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

# 4. Script that mention the overload and the transmission of information to the zabbix server
On the windows machine, go to C:\ and create a folder named "script"

Make sure you have enable the file extensions in your file explorer

Create 2 files under "C:/script"
```
cpu_last_state.txt
cpu_log.bat
cpu_log.txt
```
Put the folling script in cpu_log.txt file
```
@echo off
setlocal enabledelayedexpansion

set "STATE_FILE=C:\script\cpu_last_state.txt"
set "LOG_FILE=C:\Program Files\Zabbix Agent\zabbix_agentd.log"
set "THRESHOLD=90"
set "CURRENT="

:: Récupère la charge CPU actuelle
for /f "tokens=2 delims==." %%A in ('wmic cpu get loadpercentage /value ^| findstr LoadPercentage') do (
    set /a CURRENT=%%A
)

:: Vérifie que la valeur a bien été récupérée
if not defined CURRENT (
    echo 0
    exit /b
)

:: Si le fichier d'état n'existe pas, on l'initialise
if not exist "%STATE_FILE%" (
    echo !CURRENT! > "%STATE_FILE%"
)

:: Lecture de l'ancienne valeur
set /p PREV=<"%STATE_FILE%"

:: Vérifie que PREV est bien un nombre
for /f %%B in ("!PREV!") do set /a PREV=%%B

:: Log si le seuil est franchi à la hausse
if !CURRENT! GEQ %THRESHOLD% (
    if !PREV! LSS %THRESHOLD% (
        echo !DATE! !TIME! CPU usage rose above %THRESHOLD%%: !CURRENT!%% >> "%LOG_FILE%"
    )
) else (
    if !PREV! GEQ %THRESHOLD% (
        echo !DATE! !TIME! CPU usage fell below %THRESHOLD%%: !CURRENT!%% >> "%LOG_FILE%"
    )
)

:: Mise à jour de l'état courant
echo !CURRENT! > "%STATE_FILE%"

:: Affiche la valeur actuelle pour Zabbix
echo !CURRENT!
```

## 4.1. Zabbix agent configuration

Add the line in "C:\Program Files\Zabbix Agent2\zabbix_agent2.conf"
```
UserParameter=cpu.util.custom,C:\Script\cpu_log.bat
Timeout=10
```
Before going back to the webui, go to the CLI of the zabbix-server, open /etc/zabbix/zabbix_server.conf and set the line  <StartVMwareCollector=2>

## 4.2. Host item configuration

- Data collection>Hosts>
- Click on "Items" of the windows host <MON1-CLI-WIN>
- Create item
```
Name = CPU trigger
Type = Zabbix agent
Key = cpu.util.custom
Type of information = Numeric (unsigned)
Update interval = 5s
```

# Test CPU alert

**Install software**
https://downloads.jam-software.de/heavyload/HeavyLoad-x64-Setup.exe

## Upper than 90%
- Run .\Downloads\HeavyLoad-x64-Setup.exe
- Tab Test Options: uncheck Stress GPU
- Check CPU Usage on interface
- Click play button to stress cpu
- Open Powershell in admin
- cd ../..
- cd "Program Files\Zabbix Agent"
- cat .\zabbix_agentd.log

## Lesser thant 90%
- Click stop button to unstress cpu
- cat .\zabbix_agentd.log
