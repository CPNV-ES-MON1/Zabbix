Mon 1 - Zabbix documentation <br>
ICT - GGR - RDI

# Problems and solutions

## Table of content
- [1. Commun debugg session](#1-commun-debugg-session)
  - [1.1. General ifnformations:](#11-general-ifnformations)
  - [1.2. Debugg proposition:](#12-debugg-proposition)
  - [1.3. Used commands:](#13-used-commands)
- [2. Installation issues](#2-installation-issues)
  - [2.1. Situation](#21-situation)
  - [2.2. Solution](#22-solution)
- [3. Notification issue](#3-notification-issue)
  - [3.1. Situation](#31-situation)
  - [3.2. Solution](#32-solution)
- [4. Story missunderstanding](#4-story-missunderstanding)
  - [4.1. Situation](#41-situation)


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

<br>
<br>

# 2. Installation issues
## 2.1. Situation
As we all had our own local infrastructure and we all had to do several installations we sometimes didn't have exactly the same environement which lead us to some complication when the time to deploy on AWS came. 
The biggest differences were the following:
- agent installed: 1 instead of 2
- version of ubuntu: 22.04 instead of 24.04
- (on the documentation only) command to install the packages was for AMD instead of ARM

## 2.2. Solution
Some of those issues made us lose quite some time because we weren't aware of them. As we kept exchanging VMs if one of us made a mistake the others were very alike to have it too without knowing it.

Thanks to the test session before the deployement on AWS we could correct those mistakes. As we decided for this test session to strictly follow the documentation, each time something was wrong we could see it and correct it.

It was not a big deal and most of the time it was inadvertance mistakes but it sometimes took us quite a while to find out what was the issue and how to fix it.

<br>
<br>

# 3. Notification issue
## 3.1. Situation
Setting up automatic notifications was something new for us. We wanted to be very sure of what we were doing and we read a lot of different doc to know what we were doing which cause us to take genrealy a lot of time to set that up.  Despite this we steal couldn't figure out why our texting app (matrix) couldn't recevie automatic notification from zabbix

## 3.2. Solution
We wpent more than a full day on those notification, we finally decided to make a diagram and draw everything we understood (This diagram is available on "Doc/Schemas/diagram-notifications.md"). Thanks to that we could see where the issue was. We actually needed to enable zabbix's scripts for the webhook to be able to work proprely. An information that none f the website we checkeck were talking about. 

<br>
<br>

# 4. Story missunderstanding
## 4.1. Situation
The story "Scenario - service monitoring - stop detection" was not understood well. As the story mentiones a monitoring of a MySQL we thought we were supposed to monitor the MySQL that we have installed in the begining (which is on the zabbix server). It turned out that we actually needed to create another MySQL server on the Linux client and monitor this one.

Not a big problem but it delayed the moment where we could do this story.
