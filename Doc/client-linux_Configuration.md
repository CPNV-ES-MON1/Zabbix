# instalation des packets
```
pour commencer on va telecherarger les packet et les decompréser
```
**input**
```
wget https://repo.zabbix.com/zabbix/7.2/release/debian/pool/main/z/zabbix-release/zabbix-release_latest_7.2+debian11_all.deb
```
**outpur**
```
--2025-06-04 10:46:01--  https://repo.zabbix.com/zabbix/7.2/release/debian/pool/main/z/zabbix-release/zabbix-release_latest_7.2+debian11_all.deb
Resolving repo.zabbix.com (repo.zabbix.com)... 178.128.6.101, 2604:a880:2:d0::2062:d001
Connecting to repo.zabbix.com (repo.zabbix.com)|178.128.6.101|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 7288 (7.1K) [application/octet-stream]
Saving to: ‘zabbix-release_latest_7.2+debian11_all.deb.2’

zabbix-release_latest_7.2+debian11_all.deb.2                100%[========================================================================================================================================>]   7.12K  --.-KB/s    in 0s

2025-06-04 10:46:02 (221 MB/s) - ‘zabbix-release_latest_7.2+debian11_all.deb.2’ saved [7288/7288]
```
**input**
```
 sudo dpkg -i zabbix-release_latest_7.2+debian11_all.deb
```
**output**
```
(Reading database ... 141484 files and directories currently installed.)
Preparing to unpack zabbix-release_latest_7.2+debian11_all.deb ...
Unpacking zabbix-release (1:7.2-1+debian11) over (1:7.2-1+debian11) ...
Setting up zabbix-release (1:7.2-1+debian11) ...
```
**input**
```
sudo apt update
```
**output**
```
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
All packages are up to date.
```
# installation de l'agent
**input**
```
sudo apt install zabbix-agent
```
**output**
```
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
zabbix-agent is already the newest version (1:7.2.7-1+debian11).
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```
# restart et enable le service
**input**
```
sudo systemctl restart zabbix-agent
```
**ouput**
```
/
```
**input**
```
sudo systemctl enable zabbix-agent
```
**ouput**
```
Synchronizing state of zabbix-agent.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable zabbix-agent
```
# configuration de l'agent
**dans etc/zabbix/zabbix_agentd.conf**
```
on vérifie que ces paramètres sois bien configuré:
```
**paramètres**
```
Server=192.168.153.140
ListenPort=10050
Hostname=Zabbix-client
```

# configuration sur zabbix

## ajout de l'host
```
monitoring>host on va cliquer sur "create host"
```
**onglet host**
```
dans l'onglet host on va lui mettre sont hostname zabbix, sont visible name.
ensuite sous template on choisis le template "linux by zabbix agent"
!!ATTENTION!!
il existe un "linux by zabbix agent active" il ne faut pas le prendre car le server actif de l'agent n'a pas été configuré

on le met ensuite dans un Host Groupe (exemple avec le groupe "virtual machine")

on lui aoute une interface qui est le moen de communication entre le zabbix server et l'agent et choisisant le type agent.
dans l'insterface on spécifie l'IP et le port qu'utilise l'agent.

```
**onglet inventory**
```
ici on passe l'inventaire en mode automatique sinon on ne pourra pas avoir cet host sous inventory>host dans zabbix
```
## configuration de l'item
```
sous Data collection>hosts on clique sur items de notre host.

ici on cherche l'item "CPU utilisation" et on clique dessus.

là on modifie l'option "Update interval" pour mettre 5s
```
## ajout de l'host sur ele dashboard avec l'item du CPU
```
sous Dashboard on va cliquer sur les paramètre de l'onglet ou se trouve deja notre server zabbix.

ici on va ajouter notre client linux sous "Hosts" et on va ajouter une collonne avec l'item "CPU utilization"
```
