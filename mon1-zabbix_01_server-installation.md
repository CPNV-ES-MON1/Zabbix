Mon 1 - Zabbix documentation <br>
ICT - GGR - RDI

# Server installation

## Table of content

- [1. Prerequisites](#1-prerequisites)
- [2. Installation](#2-installation)
  - [2.1. Update repo \& upgrade packages](#21-update-repo--upgrade-packages)
  - [2.2. List all installed packages](#22-list-all-installed-packages)
  - [2.3. Get latest version of zabbix server package](#23-get-latest-version-of-zabbix-server-package)
    - [2.3.1. Dowload packets](#231-dowload-packets)
    - [2.3.2. Install the packets](#232-install-the-packets)
    - [2.3.3. Update packets](#233-update-packets)
  - [2.4. List all installed packages](#24-list-all-installed-packages)
  - [2.5. List differences between 2 files](#25-list-differences-between-2-files)
    - [2.5.1. Save the diffrences in differences-before-after-zabbix.txt](#251-save-the-diffrences-in-differences-before-after-zabbixtxt)
    - [2.5.2. Check the result](#252-check-the-result)
  - [2.6. Install Zabbix server, frontend, agent2](#26-install-zabbix-server-frontend-agent2)
  - [2.7. Install mysql-server](#27-install-mysql-server)

<br>
<br>
<br>

# 1. Prerequisites

| Zabbix Version | OS distribution | OS version    | Zabbix component          | Database | Web server |
| -------------- | --------------- | ------------- | ------------------------- | -------- | ---------- |
| 7.0 LTS        | Ubuntu          | 24.04 (noble) | Server, Frontend, Agent 2 | MySQL    | Ngnix      |
|                |                 |               |                           |          |            |

Once the VM in installed, don't forget to make a snapshot to prevent having to reinstall it in case of an issue.
<br>
<br>

# 2. Installation
## 2.1. Update repo & upgrade packages
**Input**
```
sudo apt update && sudo apt upgrade
```
**Output**
```
...
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

## 2.2. List all installed packages
**Input**
```
sudo apt list --installed > all-packages-before-zabbix.txt
```
**Output**
```
WARNING: apt does not have a stable CLI interface. Use with caution in scripts.
```

See in this text file: /home/all-packages-before-zabbix.txt

## 2.3. Get latest version of zabbix server package
### 2.3.1. Dowload packets
**Input**
```
wget https://repo.zabbix.com/zabbix/7.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_latest_7.0+ubuntu24.04_all.deb
```
**Output**
```
--2025-06-08 16:14:11--  https://repo.zabbix.com/zabbix/7.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_latest_7.0+ubuntu24.04_all.deb
Resolving repo.zabbix.com (repo.zabbix.com)... 178.128.6.101, 2604:a880:2:d0::2062:d001
Connecting to repo.zabbix.com (repo.zabbix.com)|178.128.6.101|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 8092 (7.9K) [application/octet-stream]
Saving to: ‘zabbix-release_latest_7.0+ubuntu24.04_all.deb.1’

zabbix-release_latest_7.0+ubuntu24.04_al 100%[=================================================================================>]   7.90K  --.-KB/s    in 0s

2025-06-08 16:14:12 (404 MB/s) - ‘zabbix-release_latest_7.0+ubuntu24.04_all.deb.1’ saved [8092/8092]
```

### 2.3.2. Install the packets
**Input**
```
sudo dpkg -i zabbix-release_latest_7.0+ubuntu24.04_all.deb
```
**Output**
```
Selecting previously unselected package zabbix-release.
(Reading database ... 86853 files and directories currently installed.)
Preparing to unpack zabbix-release_latest_7.0+ubuntu24.04_all.deb ...
Unpacking zabbix-release (1:7.0-2+ubuntu24.04) ...
Setting up zabbix-release (1:7.0-2+ubuntu24.04) ..
```

### 2.3.3. Update packets
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

## 2.4. List all installed packages
**Input**
```
sudo apt list --installed > all-packages-after-zabbix.txt
```
**Output**
```
WARNING: apt does not have a stable CLI interface. Use with caution in scripts.
```
See in this text file: /home/all-packages-after-zabbix.txt

## 2.5. List differences between 2 files
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

### 2.5.1. Save the diffrences in differences-before-after-zabbix.txt
**Input**
```
diff -u all-packages-before-zabbix.txt all-packages-after-zabbix.txt > differences-before-after-zabbix.txt
```
**Output** - No output

### 2.5.2. Check the result
**Input**
```
nano differences-before-after-zabbix.txt
```

## 2.6. Install Zabbix server, frontend, agent2
**Input**
```
 sudo apt install zabbix-server-mysql zabbix-frontend-php zabbix-nginx-conf zabbix-sql-scripts zabbix-agent2
```
**Output**
```
no error displayed
```

## 2.7. Install mysql-server
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
