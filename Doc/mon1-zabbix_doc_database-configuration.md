Mon 1 - Zabbix documentation <br>
ICT - GGR - RDI

# Database configuration

# Table of content


- [1. Database creation](#1-database-creation)
  - [1.1. Connect to MySQL](#11-connect-to-mysql)
  - [1.2. Create databse](#12-create-databse)
  - [1.3. Create user](#13-create-user)
  - [1.4. Define new user as Admin](#14-define-new-user-as-admin)
  - [1.5. Enable trusted function creators and quit MySQL](#15-enable-trusted-function-creators-and-quit-mysql)
  - [1.6. Set up the schema and import the data into the zabbix database](#16-set-up-the-schema-and-import-the-data-into-the-zabbix-database)
  - [1.7. Disable log\_bin\_trust\_function\_creators option after importing database schema.](#17-disable-log_bin_trust_function_creators-option-after-importing-database-schema)
  - [1.8. Modification des fichiers de configuration](#18-modification-des-fichiers-de-configuration)
    - [1.8.1. zabbix\_server.conf](#181-zabbix_serverconf)
    - [1.8.2. nginx.conf](#182-nginxconf)
- [2. Restart services](#2-restart-services)
- [3. Enable services](#3-enable-services)

<br>
<br>

# 1. Database creation
## 1.1. Connect to MySQL

**Input**
```
mysql -uroot -p
```
**Output** - No output
## 1.2. Create databse
**Input**
```
create database zabbix character set utf8mb4 collate utf8mb4_bin;
```
**Output**
```
Query OK, 1 row affected (0.01 sec)
```

## 1.3. Create user
**Input**
```
create user zabbix@localhost identified by 'enter-your-password';
```
**Output**
```
Query OK, 0 rows affected (0.02 sec)
```

## 1.4. Define new user as Admin
**Input**
```
grant all privileges on zabbix.* to zabbix@localhost;
```
**Output**
```
Query OK, 0 rows affected (0.00 sec)
```

## 1.5. Enable trusted function creators and quit MySQL
**Input**
```
set global log_bin_trust_function_creators = 1;
```
**Output**
```
Query OK, 0 rows affected, 1 warning (0.00 sec)
```

**Input**
```
quit;
```
**Output**
```
Bye!
```

## 1.6. Set up the schema and import the data into the zabbix database
**Input**
```
zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql --default-character-set=utf8mb4 -uzabbix -p zabbix
```
**Output**
```
Enter password: 
```

## 1.7. Disable log_bin_trust_function_creators option after importing database schema.

**Input**
```
mysql -uroot -p
```
**Output** - no output

**Input**
```
set global log_bin_trust_function_creators = 0;
```
**Output**
```
Query OK, 0 rows affected, 1 warning (0.00 sec)
```

**Input**
```
quit;
```
**Output**
```
Bye
```

## 1.8. Modification des fichiers de configuration
### 1.8.1. zabbix_server.conf
```
nano /etc/zabbix/zabbix_server.conf

 ### Option: DBPassword
#       Database password.
#       Comment this line if no password is used.
#
# Mandatory: no
# Default:
 DBPassword=[password]
```
### 1.8.2. nginx.conf
```
nano /etc/zabbix/zabbix_server.conf

nano /etc/zabbix/nginx.conf

server {
        listen          8080;
        server_name     mon1-zabbix.local;
```
# 2. Restart services
**Input**
```
systemctl restart zabbix-server zabbix-agent nginx php8.3-fpm
```
**Output** - no output

# 3. Enable services
**Input**
```
systemctl enable zabbix-server zabbix-agent nginx php8.3-fpm
```
**Output**
```
Synchronizing state of zabbix-server.service with SysV service script with /usr/lib/systemd/systemd-sysv-install.
Executing: /usr/lib/systemd/systemd-sysv-install enable zabbix-server
Synchronizing state of zabbix-agent.service with SysV service script with /usr/lib/systemd/systemd-sysv-install.
Executing: /usr/lib/systemd/systemd-sysv-install enable zabbix-agent
Synchronizing state of nginx.service with SysV service script with /usr/lib/systemd/systemd-sysv-install.
Executing: /usr/lib/systemd/systemd-sysv-install enable nginx
Synchronizing state of php8.3-fpm.service with SysV service script with /usr/lib/systemd/systemd-sysv-install.
Executing: /usr/lib/systemd/systemd-sysv-install enable php8.3-fpm
Created symlink /etc/systemd/system/multi-user.target.wants/zabbix-server.service → /usr/lib/systemd/system/zabbix-server.service.
root@ubuntu24-mon1:/etc/zabbix# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute
       valid_lft forever preferred_lft forever
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UNKNOWN group default qlen 1000
    link/ether 00:0c:29:0b:78:15 brd ff:ff:ff:ff:ff:ff
    altname enp2s1
    inet 192.168.153.140/24 metric 100 brd 192.168.153.255 scope global dynamic ens33
       valid_lft 1315sec preferred_lft 1315sec
    inet6 fe80::20c:29ff:fe0b:7815/64 scope link
       valid_lft forever preferred_lft forever
```

Pour accéder à zabbix: **\<ip du server>:8080**
