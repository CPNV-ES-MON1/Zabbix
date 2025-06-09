# in /etc/zabbix/zabbix_agentd.conf
add the following line the the config
```
	UserParameter=mysql.service.status,systemctl is-active mysql.service
```
sudo systemctl restart zabbix-agent
# in zabbix webui
under Data collection>hosts>[host item]>"create item"
|---------------------------------------------------||
|Field                  |	Value                     |
|Name	                  | MySQL Service Status      |
|Type                 	| Zabbix agent              |
|Key	                  | mysql.service.status      |
|Type of information	  | Log                       |
|Host interface         | 127.0.0.1:10050           |
|Update interval	      | 30s                       |
|log format             |	date +"%d.%m.%Y %I:%M %p" |
|---------------------------------------------------||
Save the item.
