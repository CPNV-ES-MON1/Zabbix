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
