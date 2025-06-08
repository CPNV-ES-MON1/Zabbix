# Sources
## Set up matrix notifications
https://github.com/jooola/zabbix-matrix-webhook?tab=readme-ov-file
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
- Room 1 :
- Name : zabbix-room
- Internal room ID : ABAB

#### Invite the bot in the room
```
click on .../Invite/Enter the bot username (zabbix-mon1)
```

### Set up an encrypted room on this account
- Room 2 :
- Name : zabbix-room-unencrypted
- Internal room ID : BABA

#### Invite the bot in the room
```
click on .../Invite/Enter the bot username (zabbix-mon1)
```

# Zabbix webhook configuration

## Create from scratch webhook
- Alerts/Media types/Create media type/
- Name : matrix
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
|event_url||
|http_proxy||
|matrix_room|{ALERT.SENDTO}|
|matrix_token|ZYZY|
|matrix_url|https://matrix-client.matrix.org|
- Script : look at `mon1-zabbix_doc_notificatin_script` or https://github.com/jooola/zabbix-matrix-webhook/blob/main/latest/matrix.js
- Timeout : 30s

## Create/configure the matrix media type for a user that represent the webhook
### Retrieve room ID
```
click on .../Settings/Advanced/copy Internal room ID
```
### Configuration for the user gir
Users/gir/Media/Add
- Type: matrix
- Send to: Internal room ID
- When active: <default choices>
- Use if severity: <default choices>

# Test webhook
- Alerts/Media types/locate the relevant webhook <matrix>/click on Test/click on Test
- Check on the element software
