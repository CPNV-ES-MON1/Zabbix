# Installation of the zabbix agent

win+x / a
```
curl -O 'C:\Users\Administrateur\Downloads\zabbix-agent.msi' "https://cdn.zabbix.com/zabbix/binaries/stable/7.0/7.0.12/zabbix_agent2-7.0.12-windows-amd64-openssl.msi"

.\zabbix-agent.msi
```

# configuration on the client

**installation folder**
```
after accepting the license we let the parameter of the next page by default.
```
**zabbix server configuration**
```
hostname : <MON1-CLI-WIN>
ip : [server ip]
port : 10050 by default
PSK : no
add agent location PATH : yes
```
# configuration on zabbix server

## adding the host
```
monitoring>host>create host
```
**Tab host**
```
hostname : <MON1-CLI-WIN>
visible name : <MON1-CLI-WIN>
click on template
search for windows by zabbix agent
host groupe : virtual machine
interface:
  select: agent
  ip : [host ip]
  port : 10050
```

**Tab inventory**
```
use the automatic option
```

## item configuration
```
Data collection>hosts>
- Click on "Items" of the windows host MON1-CLI-WIN (be sure not to have any filters and click Apply)

select "CPU utilization"
- Update interval : 5s
```

## add the host to the dashboard with the item
```
Dashboards>Top hosts by CPU utilization>Setting icon
- Hosts: Select>write Virtual machines>select MON1-CLI-WIN
- Columns: Add
  - Name: CPU utilization
  - Item name: Select>select CPU utilization
```
# script creaation on the windows client
create 3 file under "C:/script"
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
## zabbix agent configuration
add the line in "C:\programes Files\Zabbix Agent\zabbix_agentd.conf"
```
UserParameter=cpu.util.custom, C:\Script\cpu_log.bat
```
## host item configuration
add an item under the windows host
```
Type = Zabbix agent
name = CPU trigger
key = cpu.util.custom
Type of information = Numeric
Units = %
Update interval = 5s
```
