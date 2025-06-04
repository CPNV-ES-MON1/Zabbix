# Sources https://github.com/jooola/zabbix-matrix-webhook?tab=readme-ov-file

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
- Password : XYXY
- Access Token : YXYX
- Recovery Key : XXYY

### Set up an encrypted room on this account
- Room 1 :
- Name : zabbix-room
- Internal room ID : YZYZ

#### Invite the bot in the room
```
click on .../Invite/Enter the bot username (zabbix-mon1)
```
#### Retrieve room ID
```
click on .../Settings/Advanced/copy Internal room ID
```
### Set up an encrypted room on this account
- Room 2 :
- Name : zabbix-room-unencrypted
- Internal room ID : ZYZY

# Zabbix webhook configuration
