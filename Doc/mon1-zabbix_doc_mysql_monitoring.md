## installation in cli linux
```
sudo apt-get install mariadb-server
```

# in /etc/zabbix/zabbix_agent2.conf

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

# in zabbix webui
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

# Test if toggle from on to off and off to on
```
sudo systemctl status mysql.service
sudo systemctl stop mysql.service
sudo systemctl status mysql.service
sudo systemctl restart mysql.service
sudo systemctl status mysql.service
```
