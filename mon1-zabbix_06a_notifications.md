Mon 1 - Zabbix documentation <br>
ICT - GGR - RDI

# Notifications

## Table of content

- [1. Sources](#1-sources)
  - [1.1. Matrix notification diagram](#11-matrix-notification-diagram)
- [2. Matrix configuration](#2-matrix-configuration)
  - [2.1. Create a 1st Matrix account on app.element.io (bot)](#21-create-a-1st-matrix-account-on-appelementio-bot)
    - [2.1.1. Retrieve the bot's access token](#211-retrieve-the-bots-access-token)
  - [2.2. Create a 2nd Matrix account on app.element.io (user account)](#22-create-a-2nd-matrix-account-on-appelementio-user-account)
    - [2.2.1. Set up an encrypted room on this account](#221-set-up-an-encrypted-room-on-this-account)
      - [2.2.1.1. Invite the bot in the room](#2211-invite-the-bot-in-the-room)
- [3. Zabbix webhook configuration](#3-zabbix-webhook-configuration)
  - [3.1. Create from scratch webhook](#31-create-from-scratch-webhook)
  - [3.2. Create/configure the matrix media type for a user that represent the webhook](#32-createconfigure-the-matrix-media-type-for-a-user-that-represent-the-webhook)
    - [3.2.1. Create a zabbix user named "matrix"](#321-create-a-zabbix-user-named-matrix)
    - [3.2.2. Test webhook](#322-test-webhook)
- [4. Zabbix trigger configuration](#4-zabbix-trigger-configuration)
  - [4.1. Templates configuration](#41-templates-configuration)
  - [4.2. Authorize scripts in Zabbix](#42-authorize-scripts-in-zabbix)
  - [4.3. Test to stress CPU](#43-test-to-stress-cpu)
    - [4.3.1. On the client (windows)](#431-on-the-client-windows)
    - [4.3.2. On matrix room zabbix-notifications](#432-on-matrix-room-zabbix-notifications)



# 1. Sources
## 1.1. Matrix notification diagram
https://patrick.cloke.us/posts/2023/05/08/matrix-push-rules-notifications/
https://patrick.cloke.us/images/matrix-push-rules-and-notifications/mobile-push-flow.png

# 2. Matrix configuration

## 2.1. Create a 1st Matrix account on app.element.io (bot)
- Username : zabbix-mon1
- Email : ianalain.clot@eduvaud.ch
- Password : XYXY
- Display name : zabbix-mon1
- Access Token : YXYX
- Recovery Key : XXYY

### 2.1.1. Retrieve the bot's access token
! This token change every time you logout/login, to be more permanent, download and install the app from https://element.io/download
```
Icon from the account/All settings/Help & About/Advanced/Access Token/copy this access token
```

## 2.2. Create a 2nd Matrix account on app.element.io (user account)
- Username : girigr
- Email : pd34glp@eduvaud.ch
- Password : YZYZ
- Access Token : ZYZY
- Recovery Key : YYZZ

### 2.2.1. Set up an encrypted room on this account
- Name : zabbix-notifications
- Internal room ID : ABAB

#### 2.2.1.1. Invite the bot in the room
```
click on .../Invite/Enter the bot username (zabbix-mon1)
```

# 3. Zabbix webhook configuration
https://github.com/jooola/zabbix-matrix-webhook?tab=readme-ov-file

## 3.1. Create from scratch webhook
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
|enable_icons|false|
|event_is_problem|{EVENT.VALUE}|
|event_is_update|{EVENT.UPDATE.STATUS}|
|event_severity|{EVENT.SEVERITY}|
|matrix_room|BABA|
|matrix_token|YXYX|
|matrix_url|https://matrix-client.matrix.org|
- Script : look at `mon1-zabbix_06b_notificatin_script.js` or https://github.com/jooola/zabbix-matrix-webhook/blob/main/latest/matrix.js
- Timeout : 30s

/Message templates
- Message templates: Add
  - Message type: Problem
  - subject: Problem: surcharge de CPU
  - message: CPU en surcharge sur {HOST.NAME} l'infrastructure risque des ralentissements.

## 3.2. Create/configure the matrix media type for a user that represent the webhook
### 3.2.1. Create a zabbix user named "matrix"
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
- SuperAdmin role

### 3.2.2. Test webhook
- Alerts/Media types/locate the relevant webhook <matrix>/click on Test/click on Test
- Check on the element software with 2nd Matrix account connected

Output
```
✅ {ALERT.SUBJECT}
{ALERT.MESSAGE}
```

# 4. Zabbix trigger configuration
Alerts/Actions/Trigger actions/Create action

/Action
- Name: CPU-alert-matrix
- Conditions: Add/
  - Type: Trigger
  - Operator: equals
  - Trigger source: Host
  - Triggers: Select
    - Host: MON1-CLI-WIN
      - Windows: High CPU utilization

/Operations
- Operations: Add
  - Send to users: matrix
  - Send to media type: matrix-notifications
- Uncheck:
  - Pause operations for symptom problems
  - Pause operations for suppressed problems

## 4.1. Templates configuration
Data collection/Templates/Windows by Zabbix agent

/Items
- CPU utilization
  - Update interval: 5s

/Triggers
- Windows: High CPU utilization
  - Event name: Windows: High CPU utilization (over {$CPU.UTIL.CRIT}% for 30s)
  - min(/Windows by Zabbix agent/system.cpu.util,30s)>{$CPU.UTIL.CRIT}

## 4.2. Authorize scripts in Zabbix
On the server
```
sudo nano /etc/zabbix/zabbix_server.conf
```
ctrl+w -> enableglo
```
EnableGlobalScripts=1
```

## 4.3. Test to stress CPU
### 4.3.1. On the client (windows)
Make a CPU stress with the same software we used in the prvious documentation ("mon1-zabbix_04_client-windows-configuration.md")

### 4.3.2. On matrix room zabbix-notifications
Output
```
Problem: Windows: High CPU utilization (over 90% for 30s)
Problem started at 12:53:54 on 2025.06.09
Problem name: Windows: High CPU utilization (over 90% for 30s)
Host: MON1-CLI-WIN
Severity: Warning
Operational data: Current utilization: 99.98 %
```
