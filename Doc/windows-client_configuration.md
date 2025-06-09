# instalation of the zabbix agent
```
https://www.zabbix.com/download_agents
```
# configuration of the agent

**installation folder**
```
after accepting the license we let the parameter of the nexte page by default.
```
**zabbix server configuration**
```
hostname : [name]
ip : [server ip]
port : 10050 by default
PSK : yes
add agent location PATH : yes
```
# configuration on zabbix

## adding the host
```
monitoring>host>create host
```
**host page**
```
hostname : [name]
visible name : [name]
template : windows by zabbix agent
host groupe : virtual machine
interface:
  select agent
  ip : [host ip]
  port : 10050
```
**inventory page**
```
use the automatic option
```
## item configuration
```
Data collection>hosts>[windows host item]

select "CPU utilization"
  Update interval : 5s
```
## add the host to the dashboard with the item
```
Dashboard>setting
add the host under "Hosts"
add "CPU utilization" under the collumns
```

