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

# Update repo & upgrade packages
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
# List all installed packages
**Input**
```
sudo apt list --installed > all-packages-before-zabbix.txt
```
**Output**
```
WARNING: apt does not have a stable CLI interface. Use with caution in scripts.
```
See in this text file: /home/all-packages-before-zabbix.txt

# Get latest version of zabbix server package
**Input**
```
wget https://repo.zabbix.com/zabbix/7.0/debian/pool/main/z/zabbix-release/zabbix-release_latest_7.0+debian12_all.deb
```
**Output**
```
--2025-05-13 09:00:32--  https://repo.zabbix.com/zabbix/7.0/debian/pool/main/z/zabbix-release/zabbix-release_latest_7.0+debian12_all.deb
Resolving repo.zabbix.com (repo.zabbix.com)... 178.128.6.101, 2604:a880:2:d0::2062:d001
Connecting to repo.zabbix.com (repo.zabbix.com)|178.128.6.101|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 8096 (7.9K) [application/octet-stream]
Saving to: ‘zabbix-release_latest_7.0+debian12_all.deb’

zabbix-release_latest_7.0+debian12_all.deb           100%[=====================================================================================================================>]   7.91K  --.-KB/s    in 0s      

2025-05-13 09:00:35 (136 MB/s) - ‘zabbix-release_latest_7.0+debian12_all.deb’ saved [8096/8096]
```
**Input**
```
sudo dpkg -i zabbix-release_latest_7.0+debian12_all.deb
```
**Output**
```
Selecting previously unselected package zabbix-release.
(Reading database ... 33663 files and directories currently installed.)
Preparing to unpack zabbix-release_latest_7.0+debian12_all.deb ...
Unpacking zabbix-release (1:7.0-2+debian12) ...
Setting up zabbix-release (1:7.0-2+debian12) ...
```
# List all installed packages
**Input**
```
sudo apt list --installed > all-packages-after-zabbix.txt
```
**Output**
```
WARNING: apt does not have a stable CLI interface. Use with caution in scripts.
```
See in this text file: /home/all-packages-after-zabbix.txt

# List differences between 2 files
**Input**
```
diff -u all-packages-before-zabbix.txt all-packages-after-zabbix.txt
diff -u all-packages-before-zabbix.txt all-packages-after-zabbix.txt > differences-before-after-zabbix.txt
```
# Install Zabbix server, frontend, agent2
**Input**
```
sudo apt install zabbix-server-mysql zabbix-frontend-php zabbix-nginx-conf zabbix-sql-scripts zabbix-agent2
```
# Install Zabbix agent 2 plugin
**Input**
```
apt install zabbix-agent2-plugin-mssql
```
# Create initial database
**Input**
```
mysql -uroot -p
```
enter your-nice-password
**Input**
```
create database zabbix character set utf8mb4 collate utf8mb4_bin; 
create user cpnv@localhost identified by 'your-nice-password'; 
grant all privileges on zabbix.* to cpnv@localhost; 
set global log_bin_trust_function_creators = 1; 
quit; 
```
**Input**
# ???
```
zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql --default-character-set=utf8mb4 -ucpnv -p zabbix 
```
# Disable log_bin_trust_function_creators option after importing database schema
**Input**
```
# mysql -uroot -p
enter your-nice-password
set global log_bin_trust_function_creators = 0;
quit; 
```
# Configure the database for Zabbix server
**Input**
```
sudo nano /etc/zabbix/zabbix_server.conf
DBPassword=your-nice-password
```
# Start Zabbix server and agent processes
**Input**
```
systemctl restart zabbix-server zabbix-agent2 nginx php8.2-fpm
systemctl enable zabbix-server zabbix-agent2 nginx php8.2-fpm
```
# Open Zabbix UI web page
**Input**
```
http://192.168.0.144
```


# Note pendant la session de debug
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
