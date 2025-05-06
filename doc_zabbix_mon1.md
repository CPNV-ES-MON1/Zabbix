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

# List all installed packages
```
sudo apt list --installed > all-packages-before-zabbix.txt
```
# Update repo & upgrade packages
```
apt update && apt upgrade
```
# Get latest version of zabbix server package
```
wget https://repo.zabbix.com/zabbix/7.0/debian/pool/main/z/zabbix-release/zabbix-release_latest_7.0+debian12_all.deb
dpkg -i zabbix-release_latest_7.0+debian12_all.deb
```
# List all installed packages
```
sudo apt list --installed > all-packages-after-zabbix.txt
```
# List differences between 2 files
```
diff -u all-packages-before-zabbix.txt all-packages-after-zabbix.txt
diff -u all-packages-before-zabbix.txt all-packages-after-zabbix.txt > differences-before-after-zabbix.txt
```
# Install Zabbix server, frontend, agent2
```
sudo apt install zabbix-server-mysql zabbix-frontend-php zabbix-nginx-conf zabbix-sql-scripts zabbix-agent2
```
# Install Zabbix agent 2 plugin
```
apt install zabbix-agent2-plugin-mssql
```
# Create initial database
```
mysql -uroot -p
```
