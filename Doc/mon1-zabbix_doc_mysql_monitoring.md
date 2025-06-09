# in /etc/zabbix/zabbix_agentd.conf
add the following line the the config
```
	UserParameter=mysql.service.status,systemctl is-active mysql.service
```
sudo systemctl restart zabbix-agent
# in zabbix webui
under Data collection>hosts>[host item]>"create item"


|Name|Value|
|---|---|
|alert_message|{ALERT.MESSAGE}|
|alert_subject|{ALERT.SUBJECT}|
|enable_colors|true|
|enable_icons|true|
|event_is_problem|{EVENT.VALUE}|
|event_is_update|{EVENT.UPDATE.STATUS}|
|event_severity|{EVENT.NSEVERITY}|
|matrix_room|BABA|
|matrix_token|YXYX|
|matrix_url|https://matrix-client.matrix.org|


|||
|---------------------------------------------------|----|
|Field                  |	Value                     |
|Name	                  | MySQL Service Status      |
|Type                 	| Zabbix agent              |
|Key	                  | mysql.service.status      |
|Type of information	  | Log                       |
|Host interface         | 127.0.0.1:10050           |
|Update interval	      | 30s                       |
|log format             |	date +"%d.%m.%Y %I:%M %p" |
|---------------------------------------------------|-----|
Save the item.
