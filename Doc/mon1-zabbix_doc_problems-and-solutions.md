Mon 1 - Zabbix documentation <br>
ICT - GGR - RDI

# Problems and solutions

## Table of content
- [1. Commun debugg session](#1-commun-debugg-session)
  - [1.1. General ifnformations:](#11-general-ifnformations)
  - [1.2. Debugg proposition:](#12-debugg-proposition)
  - [1.3. Used commands:](#13-used-commands)

# 1. Commun debugg session
## 1.1. General ifnformations:
The problem we're having is related to NGINX connection. It seems to be running correctly, but it's impossible to display the zabbix page. 

We previously encountered the following error, but didn't catch it:
```
ERROR 2002 (HY000) : Can't connect to local server through socket '/run/mysqld/mysqld.sock' (2)
```

This indicates that the connection to the DB through the socket is not working. We can also deduce that the connection does not go through port 3306 at all. Despite what we initially thought, this error is not linked to MySQL itself, and changing DB engine is therefore not necessarily necessary.



## 1.2. Debugg proposition:
(the 2002 error was not yet known and studied)
Our problem needs to be broken down. We're not sure what caused our problem or the initial problem (MySQL not working, which caused us to migrate to MariaDB). Here's some useful information to apply each time to validate each step.
```
- Start every new installation with an upgrade
- Check and export the list of services and packets before adding new ones, to compare after installation
- Check the hash of each newly installed rest and update afterwards.
- Check the status of each service after installation and before deletion
```


## 1.3. Used commands:
- List all services : <br> ``` systemctl list-units --type=service ```
- Check logs : <br> ``` sudo tail -f /var/log/nginx/access.log ```
- Logs filtering: <br> ``` systemctl list-units --type=service | grep Zabbix ```
- Delete packet : <br> ``` sudo apt --purge autoremove mariadb-server ```
- Install netstat: <br> ``` sudo apt install net-tools ```
- BDD connection : <br> ``` mysql - u root -p ```
