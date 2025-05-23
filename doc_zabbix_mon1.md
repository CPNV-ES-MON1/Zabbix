# Prerequisites

Zabbix version :
7.0 LTS

OS distribution :
Debian

OS version
12 (Bookworm)

Zabbix component :
Server, Frontend, Agent 2

Database :
MySQL

Web server :
Nginx

# Create a snapshot after a clean install

# Installation
## Update repo & upgrade packages
**Input**
```
sudo apt update && sudo apt upgrade
```
**Output**
```
Hit:1 http://mirror.init7.net/debian bookworm InRelease                     
Hit:2 http://mirror.init7.net/debian bookworm-updates InRelease             
Hit:3 http://security.debian.org/debian-security bookworm-security InRelease
Reading package lists... Done                 
Building dependency tree... Done
Reading state information... Done
All packages are up to date.
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Calculating upgrade... Done
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

## List all installed packages
**Input**
```
sudo apt list --installed > all-packages-before-zabbix.txt
```
**Output**
```
WARNING: apt does not have a stable CLI interface. Use with caution in scripts.
```

See in this text file: /home/all-packages-before-zabbix.txt

## Get latest version of zabbix server package
**Input**
```
wget https://repo.zabbix.com/zabbix/7.0/ubuntu-arm64/pool/main/z/zabbix-release/zabbix-release_latest_7.0+ubuntu24.04_all.deb
```
**Output**
```
--2025-05-14 08:35:20--  https://repo.zabbix.com/zabbix/7.0/ubuntu-arm64/pool/main/z/zabbix-release/zabbix-release_latest_7.0+ubuntu24.04_all.deb
Resolving repo.zabbix.com (repo.zabbix.com)... 178.128.6.101, 2604:a880:2:d0::2062:d001
Connecting to repo.zabbix.com (repo.zabbix.com)|178.128.6.101|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 8100 (7.9K) [application/octet-stream]
Saving to: ‘zabbix-release_latest_7.0+ubuntu24.04_all.deb’

zabbix-release_latest_7.0+ubuntu24.04_all.deb               100%[========================================================================================================================================>]   7.91K  --.-KB/s    in 0s

2025-05-14 08:35:21 (474 MB/s) - ‘zabbix-release_latest_7.0+ubuntu24.04_all.deb’ saved [8100/8100]
```

**Input**
```
dpkg -i zabbix-release_latest_7.0+ubuntu24.04_all.deb
```
**Output**
```
Selecting previously unselected package zabbix-release.
(Reading database ... 86853 files and directories currently installed.)
Preparing to unpack zabbix-release_latest_7.0+ubuntu24.04_all.deb ...
Unpacking zabbix-release (1:7.0-2+ubuntu24.04) ...
Setting up zabbix-release (1:7.0-2+ubuntu24.04) ..
```

**Input**
```
sudo apt update
```
**Output**
```
Hit:1 http://ch.archive.ubuntu.com/ubuntu noble InRelease
Hit:2 http://ch.archive.ubuntu.com/ubuntu noble-updates InRelease
Hit:3 http://ch.archive.ubuntu.com/ubuntu noble-backports InRelease
Hit:4 https://repo.zabbix.com/zabbix-tools/debian-ubuntu noble InRelease
Get:5 https://repo.zabbix.com/zabbix/7.0/ubuntu noble InRelease [3,969 B]
Get:6 https://repo.zabbix.com/zabbix/7.0/ubuntu noble/main Sources [21.1 kB]
Get:7 https://repo.zabbix.com/zabbix/7.0/ubuntu noble/main all Packages [8,109 B]
Get:8 https://repo.zabbix.com/zabbix/7.0/ubuntu noble/main amd64 Packages [40.5 kB]
Hit:9 http://security.ubuntu.com/ubuntu noble-security InRelease
Fetched 73.7 kB in 12s (5,946 B/s)
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
1 package can be upgraded. Run 'apt list --upgradable' to see it.
```

## List all installed packages
**Input**
```
sudo apt list --installed > all-packages-after-zabbix.txt
```
**Output**
```
WARNING: apt does not have a stable CLI interface. Use with caution in scripts.
```
See in this text file: /home/all-packages-after-zabbix.txt

## List differences between 2 files
**Input**
```
diff -u all-packages-before-zabbix.txt all-packages-after-zabbix.txt
```
**Output**
```
--- all-packages-before-zabbix.txt      2025-05-14 08:32:57.730463460 +0000
+++ all-packages-after-zabbix.txt       2025-05-14 10:07:23.242592710 +0000
@@ -76,8 +76,11 @@
 firmware-sof-signed/noble-updates,now 2023.12.1-1ubuntu1.4 all [installed,automatic]
 fontconfig-config/noble,now 2.15.0-1.1ubuntu2 amd64 [installed,automatic]
 fonts-dejavu-core/noble,now 2.37-8 all [installed,automatic]
+fonts-dejavu-extra/noble,now 2.37-8 all [installed,automatic]
 fonts-dejavu-mono/noble,now 2.37-8 all [installed,automatic]
+fonts-dejavu/noble,now 2.37-8 all [installed,automatic]
 fonts-ubuntu-console/noble,now 0.869+git20240321-0ubuntu1 all [installed,automatic]
+fping/noble,now 5.1-1 amd64 [installed,automatic]
 friendly-recovery/noble,now 0.2.42 all [installed,automatic]
 ftp/noble,now 20230507-2build3 all [installed,automatic]
 fuse3/noble,now 3.14.0-5build1 amd64 [installed,automatic]
@@ -180,8 +183,11 @@
 libcap2-bin/noble-updates,noble-security,now 1:2.66-5ubuntu2.2 amd64 [installed,automatic]
 libcap2/noble-updates,noble-security,now 1:2.66-5ubuntu2.2 amd64 [installed,automatic]
 libcbor0.10/noble,now 0.10.2-1.2ubuntu2 amd64 [installed,automatic]
+libcgi-fast-perl/noble,now 1:2.17-1 all [installed,automatic]
+libcgi-pm-perl/noble,now 4.63-1 all [installed,automatic]
 libclang-cpp18/noble-updates,now 1:18.1.3-1ubuntu1 amd64 [installed,automatic]
 libclang1-18/noble-updates,now 1:18.1.3-1ubuntu1 amd64 [installed,automatic]
+libclone-perl/noble,now 0.46-1build3 amd64 [installed,automatic]
 libcom-err2/noble-updates,now 1.47.0-2.4~exp1ubuntu4.1 amd64 [installed,automatic]
 libcrypt-dev/noble,now 1:4.4.36-4build1 amd64 [installed,automatic]
 libcrypt1/noble,now 1:4.4.36-4build1 amd64 [installed,automatic]
```

**Input**
```
diff -u all-packages-before-zabbix.txt all-packages-after-zabbix.txt > differences-before-after-zabbix.txt
```
**Output**
```
/
```

See in this text file: /home/differences-before-after-zabbix.txt

## Install Zabbix server, frontend, agent2
**Input**
```
apt install zabbix-server-mysql zabbix-frontend-php zabbix-nginx-conf zabbix-sql-scripts zabbix-agent
```
**Output**
```
no error displayed
```

## Install mysql-server
**Input**
```
sudo apt install mysql-server
```
**Output**
```
done!
update-alternatives: using /var/lib/mecab/dic/ipadic-utf8 to provide /var/lib/mecab/dic/debian (mecab-dictionary) in auto mode
Setting up libhtml-parser-perl:amd64 (3.81-1build3) ...
Setting up libhttp-message-perl (6.45-1ubuntu1) ...
Setting up mysql-server (8.0.42-0ubuntu0.24.04.1) ...
Setting up libcgi-pm-perl (4.63-1) ...
Setting up libhtml-template-perl (2.97-2) ...
Setting up libcgi-fast-perl (1:2.17-1) ...
Processing triggers for man-db (2.12.0-4build2) ...
Processing triggers for libc-bin (2.39-0ubuntu8.4) ...
Scanning processes...
Scanning linux images...

Running kernel seems to be up-to-date.

No services need to be restarted.

No containers need to be restarted.

No user sessions are running outdated binaries.

No VM guests are running outdated hypervisor (qemu) binaries on this host.
```

# Database creation
**Input**
```
mysql -uroot -p
```
**Output**
```
/
```

**Input**
```
create database zabbix character set utf8mb4 collate utf8mb4_bin;
```
**Output**
```
Query OK, 1 row affected (0.01 sec)
```

**Input**
```
create user zabbix@localhost identified by 'enter-your-password';
```
**Output**
```
Query OK, 0 rows affected (0.02 sec)
```

**Input**
```
grant all privileges on zabbix.* to zabbix@localhost;
```
**Output**
```
Query OK, 0 rows affected (0.00 sec)
```

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

## Set up the schema and import the data into the zabbix database
**Input**
```
zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql --default-character-set=utf8mb4 -uzabbix -p zabbix
```
**Output**
```
Enter password: 
```

## Disable log_bin_trust_function_creators option after importing database schema.

**Input**
```
mysql -uroot -p
```
**Output**
```
/
```

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

## Modification des fichiers de configuration
**zabbix_server.conf**
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
**Modification de nginx.conf**
```
nano /etc/zabbix/zabbix_server.conf

nano /etc/zabbix/nginx.conf

server {
        listen          8080;
        server_name     mon1-zabbix.local;
```
## Restart and enable services
**Input**
```
systemctl restart zabbix-server zabbix-agent nginx php8.3-fpm
```
**Output**
```
/
```
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

**Pour accéder à zabbix: <ip du server>:8080**

## Configure the database for Zabbix server
**Input**
```
sudo nano /etc/zabbix/zabbix_server.conf
DBPassword=<password habituel du cpnv>
```
## Start Zabbix server and agent processes
**Input**
```
systemctl restart zabbix-server zabbix-agent2 nginx php8.2-fpm
systemctl enable zabbix-server zabbix-agent2 nginx php8.2-fpm
```

# Open Zabbix UI web page
**Input**
```
http://<ip of the server>
```
## Check of pre-requisites
**Output**
```
PHP version	8.3.6	8.0.0	OK
PHP option "memory_limit"	128M	128M	OK
PHP option "post_max_size"	16M	16M	OK
PHP option "upload_max_filesize"	2M	2M	OK
PHP option "max_execution_time"	300	300	OK
PHP option "max_input_time"	300	300	OK
PHP databases support	MySQL
		OK
PHP bcmath	on		OK
PHP mbstring	on		OK
PHP option "mbstring.func_overload"	off	off	OK
PHP sockets	on		OK
PHP gd	2.3.3	2.0	OK
PHP gd PNG support	on		OK
PHP gd JPEG support	on		OK
PHP gd GIF support	on		OK
PHP gd FreeType support	on		OK
PHP libxml	2.9.14	2.6.15	OK
PHP xmlwriter	on		OK
PHP xmlreader	on		OK
PHP LDAP	on		OK
PHP OpenSSL	on		OK
PHP ctype	on		OK
PHP session	on		OK
PHP option "session.auto_start"	off	off	OK
PHP gettext	on		OK
PHP option "arg_separator.output"	&	&	OK
PHP curl	on		OK
System locale	en_US.utf8	en_US	OK
```

## Configure DB connection
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

## Settings
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

# Web interface
## Create user
```
Logged as Admin / zabbix (Super admin role)
Users/Users/Create user

Useranme: gir
Password: <password habituel du cpnv><password habituel du cpnv> (oui 2x sinon il détecte un mdp nul)
Groups: TOTP group/Zabbix administrators
Time zone: (UTC+02:00) Europe/Zurich
Theme: Dark
Refresh: 30s
Rows per page: 50
Role: Super admin role
```

## Enable MFA
```
Users/Authentication/MFA settings/Add

Type: TOTP
Name: MFA-zabbix
Hash function SHA-512
Code length: 6
```

# Session de debug
## Informations générales :
Le problème que nous rencontrons est au niveaux de la connexion à NGINX. Celui-ci à l'aire de tourner correctement mais impossible d'afficher la page de zabbix. 

Précédement nous avions renconté l'erreur suivante mais ne l'avions pas retenue:
```
ERROR 2002 (HY000) : Can't connect to local server through socket '/run/mysqld/mysqld.sock' (2)
```

Cela nous indique que la connection à travers à la BDD à travers le socket ne fonctionne pas. Nous pouvons en déduire également que la connexion ne passe absolument pas par le port 3306. Malgré ce que nous avons pensé au départ cette erreur n'est pas lié à MySQL même et changer de moteur de BDD n'est donc pas forcément nécessaire.



## Proposition de debuggage 
(l'erreur 2002 n'était pas encore connue et étudiée)
Notre problème doit être fractionné. Nous ne sommes pas certain de ce qui à causé notre problème ou le problème initial (MySQL qui ne fonctionnait pas et qui nous a fait migrer sur MariaDB). Voilà quelques informations utiles à appliquer à chaque fois afin de valider chacunes des étapes.
```
- Commencer chaques nouvelle installation par un upgrade
- Vérifier et exporter la liste des services et des packets prèsent avnt d'en ajouter afin de comparer après installation
- Vérifier le hash de chaque nouveau repos installé et faire un update après
- Vérifier l'état de chaque service après son installation et avant sa suppression
```



## Commandes utilisées :
- Lister la liste des services : <br> ``` systemctl list-units --type=service ```
- Voire les logs : <br> ``` sudo tail -f /var/log/nginx/access.log ```
- Filtrer les logs pour n'en ressortir que certains: <br> ``` systemctl list-units --type=service | grep Zabbix ```
- Supprimer un pakcet : <br> ``` sudo apt --purge autoremove mariadb-server ```
- Installer netstat: <br> ``` sudo apt install net-tools ```
- Se connecter à la BDD : <br> ``` mysql - u root -p ```
