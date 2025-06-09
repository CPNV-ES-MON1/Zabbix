# Sources
## Matrix notification diagram
https://patrick.cloke.us/posts/2023/05/08/matrix-push-rules-notifications/
https://patrick.cloke.us/images/matrix-push-rules-and-notifications/mobile-push-flow.png

# Matrix configuration

## Create a 1st Matrix account on app.element.io (bot)
- Username : zabbix-mon1
- Email : ianalain.clot@eduvaud.ch
- Password : XYXY
- Display name : zabbix-mon1
- Access Token : YXYX
- Recovery Key : XXYY

### Retrieve the bot's access token
! This token change every time you logout/login, to be more permanent, download and install the app from https://element.io/download
```
Icon from the account/All settings/Help & About/Advanced/Access Token/copy this access token
```

## Create a 2nd Matrix account on app.element.io (user account)
- Username : girigr
- Email : pd34glp@eduvaud.ch
- Password : YZYZ
- Access Token : ZYZY
- Recovery Key : YYZZ

### Set up an encrypted room on this account
- Name : zabbix-notifications
- Internal room ID : ABAB

#### Invite the bot in the room
```
click on .../Invite/Enter the bot username (zabbix-mon1)
```

# Zabbix webhook configuration
https://github.com/jooola/zabbix-matrix-webhook?tab=readme-ov-file

## Create from scratch webhook
- Alerts/Media types/Create media type/
/Media type
- Name : matrix-notifications
- Type : Webhook
- Parameters

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
- Script : look at `mon1-zabbix_doc_notificatin_script` or https://github.com/jooola/zabbix-matrix-webhook/blob/main/latest/matrix.js
- Timeout : 30s
/Message templates
- Message templates: Add
  - Message type: Problem

## Create/configure the matrix media type for a user that represent the webhook
### Create a zabbix user
Users/Users/Create user/
/User
- Username: matrix
- Password: CDCD
/Media/Add
- Type: matrix-notifications
- Send to: ABAB
- When active: 1-7,00:00-24:00
- Use if severity: select all if not already done
/Permissions
- Admin role (because it need 

### Test webhook
- Alerts/Media types/locate the relevant webhook <matrix>/click on Test/click on Test
- Check on the element software with 2nd Matrix account connected

Output
```
✅ {ALERT.SUBJECT}
{ALERT.MESSAGE}
```

# Zabbix trigger configuration
Alerts/Actions/Trigger actions/Create action
/Action
- Name: CPU-alert-matrix
- Conditions: Add/
  - Type: Trigger
  - Operator: equals
  - Trigger source: Host
  - Triggers: Select
    - Host: Zabbix server
      - Linux: High CPU utilization
/Operations
- Operations: Add
  - Send to users: matrix
  - Send to media type: matrix-notifications
- Uncheck:
  - Pause operations for symptom problems
  - Pause operations for suppressed problems

## Templates configuration
Data collection/Templates/Linux by Zabbix agent
/Items
- CPU idle time (heritage to CPU utilization)
  - Update interval: 5s

/Triggers
- Linux: High CPU utilization
  - Event name: Linux: High CPU utilization (over {$CPU.UTIL.CRIT}% for 30s)
  - min(/Linux by Zabbix agent/system.cpu.util,30s)>{$CPU.UTIL.CRIT}

## Test to stress CPU
### On server
1st terminal
```
sudo apt install -y stress
stress --cpu 2 --timeout 300s
```
2nd terminal
```
sudo apt install -y btop
btop
```

### On matrix room zabbix-notifications
Output
```
Problem: Linux: High CPU utilization (over 90% for 30s)
Problem started at 12:53:54 on 2025.06.09
Problem name: Linux: High CPU utilization (over 90% for 30s)
Host: Zabbix server
Severity: Warning
Operational data: Current utilization: 99.98 %
```
