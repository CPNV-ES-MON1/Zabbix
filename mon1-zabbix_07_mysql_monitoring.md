Mon 1 - Zabbix documentation <br>
ICT - GGR - RDI

# MySQL Monitoring

## Table of content

- [1. Installation in cli linux](#1-installation-in-cli-linux)
- [2. In /etc/zabbix/zabbix\_agent2.conf](#2-in-etczabbixzabbix_agent2conf)
- [3. In zabbix webui](#3-in-zabbix-webui)
- [4. Test if toggle from on to off and off to on](#4-test-if-toggle-from-on-to-off-and-off-to-on)
  - [4.1. Check on file](#41-check-on-file)
  - [4.2. Check in zabbix server](#42-check-in-zabbix-server)


# 1. Installation in cli linux
```
sudo apt-get install mariadb-server
```

# 2. In /etc/zabbix/zabbix_agent2.conf

add the following line the config
```
UserParameter=mysql.service.status,bash -c 'STATE_FILE=/var/log/zabbix/.mysql_last_state; LOG_FILE=/var/log/zabbix/mysql_status.log; CURRENT=$(systemctl is-active mysql.service); [ ! -f "$STATE_FILE" ] && echo "$CURRENT" > "$STATE_FILE"; PREV=$(cat "$STATE_FILE"); if [ "$CURRENT" != "$PREV" ]; then echo "$(date "+%F %T") MySQL service changed from $PREV to $CURRENT" >> "$LOG_FILE"; echo "$CURRENT" > "$STATE_FILE"; fi; echo "$CURRENT"'
```

then create the files log
```
sudo mkdir -p /var/log/zabbix
sudo touch /var/log/zabbix/mysql_status.log /var/log/zabbix/.mysql_last_state
sudo chown -R zabbix:zabbix /var/log/zabbix
sudo chmod 644 /var/log/zabbix/mysql_status.log
```

then restart the service
```
sudo systemctl restart zabbix-agent2
```

# 3. In zabbix webui
Data collection>hosts>
- Click on "Items" of the linux host "zabbix-lin-cli"
- Create item

|Field|Value|
|---------------------------------------------------|----|
|Name	                  | MySQL Service Status      |
|Type                 	| Zabbix agent              |
|Key	                  | mysql.service.status      |
|Type of information	  | Log                       |
|Host interface         | "host-linux-ip":10050     |
|Update interval	      | 30s                       |
|log time format        |	date +"%d.%m.%Y %I:%M %p" |

Add the item.

# 4. Test if toggle from on to off and off to on
## 4.1. Check n the Linux client
**Input**
```
sudo systemctl status mysql.service
sudo systemctl stop mysql.service
sudo cat /var/log/zabbix/mysql_status.log
```
**Output**
```
2025-06-10 22:10:33 MySQL service changed from  to active
2025-06-10 22:12:33 MySQL service changed from active to inactive
```
**Input**
```
sudo systemctl start mysql.service
sudo systemctl status mysql.service
```
**Output**
```
2025-06-10 22:10:33 MySQL service changed from  to active
2025-06-10 22:12:33 MySQL service changed from active to inactive
2025-06-10 22:13:33 MySQL service changed from inactive to active
```

## 4.2. Check in zabbix server
On the client enter

**Input**
```
sudo systemctl status mysql.service
sudo systemctl stop mysql.service
```
**Output** - no output

Monitoring>Hosts>"zabbix-lin-cli">Latest data>
- Name: mysql>Apply
- History
- In Value, a line was add to write inactive
```
2025-06-10 10:12:33 PM		inactive
```

On the client, enter

**Input**
```
sudo systemctl start mysql.service
sudo systemctl status mysql.service
```
**Output** - no output

Monitoring>Hosts>"zabbix-lin-cli">Latest data>
- Name: mysql>Apply
- History
- In Value, a line was add to write active
