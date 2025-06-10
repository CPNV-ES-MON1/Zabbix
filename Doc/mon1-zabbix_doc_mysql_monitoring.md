# in /etc/zabbix/zabbix_agentd.conf
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
sudo systemctl restart zabbix-agent
```
sudo systemctl restart zabbix-agent
# in zabbix webui
under Data collection>hosts>[host item]>"create item"

|Field|Value|
|---------------------------------------------------|----|
|Name	                  | MySQL Service Status      |
|Type                 	| Zabbix agent              |
|Key	                  | mysql.service.status      |
|Type of information	  | Log                       |
|Host interface         | 127.0.0.1:10050           |
|Update interval	      | 30s                       |
|log format             |	date +"%d.%m.%Y %I:%M %p" |

Save the item.
