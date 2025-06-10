Mon 1 - Zabbix documentation <br>
ICT - GGR - RDI

# Webui configuration and MFA setup

## Table of content
- [Webui configuration and MFA setup](#webui-configuration-and-mfa-setup)
  - [Table of content](#table-of-content)
- [1. Open Zabbix UI web page](#1-open-zabbix-ui-web-page)
  - [1.1. Check of pre-requisites](#11-check-of-pre-requisites)
  - [1.2. Configure DB connection](#12-configure-db-connection)
  - [1.3. Settings](#13-settings)
- [2. Web interface](#2-web-interface)
  - [2.1. Create user](#21-create-user)
  - [2.2. Enable MFA](#22-enable-mfa)


<br>
<br>

# 1. Open Zabbix UI web page
**Input**
```
http://<ip of the server>:8080
```
Then select language
## 1.1. Check of pre-requisites
**Output**
```
PHP version	  8.3.6	8.0.0	              OK
PHP option   "memory_limit"	      128M	128M	OK
PHP option "post_max_size"	      16M	  16M	  OK
PHP option "upload_max_filesize"	2M	  2M	  OK
PHP option "max_execution_time"	  300	  300  	OK
PHP option "max_input_time"	      300	  300	  OK
PHP databases support	MySQL                   OK
PHP bcmath	               on		              OK
PHP mbstring	             on                 OK
PHP option "mbstring.func_overload"	off	off	  OK
PHP sockets	               on                 OK
PHP gd	2.3.3	2.0                             OK
PHP gd PNG support	       on                 OK
PHP gd JPEG support	       on                 OK
PHP gd GIF support	       on                 OK
PHP gd FreeType support	   on	                OK
PHP libxml	2.9.14	2.6.15	                  OK
PHP xmlwriter	             on                 OK
PHP xmlreader	             on                 OK
PHP LDAP	                 on                 OK
PHP OpenSSL	               on                 OK
PHP ctype	                 on                 OK
PHP session	               on                 OK
PHP option "session.auto_start"	off	off	      OK
PHP gettext	               on		              OK
PHP option "arg_separator.output"	&	&	        OK
PHP curl	                 on              		OK
System locale	en_US.utf8	en_US	              OK
```

## 1.2. Configure DB connection
**Input**
```
Database type: MySQL
Database host: localhost
Database port: 10050 (in doc: Zabbix server uses to poll clients)
Database name: zabbix
Store credentials in: Plain text
User: zabbix
Password: <password habituel du cpnv>
```

## 1.3. Settings
**Input**
```
Zabbix server name: mon1-zabbix
Default time zone: UTC+02:00 Europe/Zurich
Default theme: Dark
```
**Output**
```
Configuration file "conf/zabbix.conf.php" created.
```

# 2. Web interface
## 2.1. Create user
```
Logged as Admin / zabbix (Super admin role)
Users/Users/Create user

Useranme: gir
Password: <password habituel du cpnv><password habituel du cpnv> (oui 2x sinon il détecte un mdp nul)
Groups: Zabbix administrators
Time zone: (UTC+02:00) Europe/Zurich
Theme: Dark
Refresh: 30s
Rows per page: 50
Role: Super admin role
```

## 2.2. Enable MFA
```
Users/Authentication/MFA settings/Add

Type: TOTP
Name: MFA-zabbix
Hash function SHA-512
Code length: 6
```

## 2.3 Create group
```
Users/Users groups/create

Group name: TOTP group
Users: Admin / gir
Frontend access: System default
Multi-factor authentication: MFA-zabbix
Enabled: Enabled
```
The next time you'll login, you'll be asked to setup your MFA.
