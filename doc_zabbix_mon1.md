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
**Input**
```
sudo apt update
```
**Output**
```
Hit:1 http://mirror.init7.net/debian bookworm InRelease                                               
Hit:2 http://mirror.init7.net/debian bookworm-updates InRelease                                       
Hit:3 http://security.debian.org/debian-security bookworm-security InRelease                     
Get:4 https://repo.zabbix.com/zabbix-tools/debian-ubuntu bookworm InRelease [2,476 B]
Get:5 https://repo.zabbix.com/zabbix/7.0/debian bookworm InRelease [3,945 B]
Get:6 https://repo.zabbix.com/zabbix-tools/debian-ubuntu bookworm/main Sources [1,166 B]              
Get:7 https://repo.zabbix.com/zabbix-tools/debian-ubuntu bookworm/main all Packages [766 B]           
Get:8 https://repo.zabbix.com/zabbix/7.0/debian bookworm/main Sources [21.1 kB]                        
Get:9 https://repo.zabbix.com/zabbix/7.0/debian bookworm/main amd64 Packages [40.8 kB]                 
Get:10 https://repo.zabbix.com/zabbix/7.0/debian bookworm/main all Packages [8,275 B]                  
Fetched 78.4 kB in 7s (11.5 kB/s)                                                                      
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
All packages are up to date.
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
```
**Output**
```
--- all-packages-before-zabbix.txt	2025-05-13 08:59:10.668967942 -0400
+++ all-packages-after-zabbix.txt	2025-05-13 09:03:49.473399013 -0400
@@ -335,6 +335,7 @@
 xdg-user-dirs/stable,now 0.18-1 amd64 [installed,automatic]
 xkb-data/stable,now 2.35.1-1 all [installed,automatic]
 xz-utils/stable-security,now 5.4.1-1 amd64 [installed]
+zabbix-release/now 1:7.0-2+debian12 all [installed,local]
 zerofree/stable,now 1.1.1-1 amd64 [installed,automatic]
 zlib1g/stable,now 1:1.2.13.dfsg-1 amd64 [installed]
 zstd/stable,now 1.5.4+dfsg2-5 amd64 [installed]
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

# Install Zabbix server, frontend, agent2
**Input**
```
sudo apt -y install zabbix-server-mysql zabbix-frontend-php zabbix-nginx-conf zabbix-sql-scripts zabbix-agent2
```
**Output**
```
no error displayed
```
# Install Zabbix agent 2 plugin
**Input**
```
sudo apt -y install zabbix-agent2-plugin-mssql
```
**Output**
```
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following NEW packages will be installed:
  zabbix-agent2-plugin-mssql
0 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
Need to get 2,412 kB of archives.
After this operation, 8,228 kB of additional disk space will be used.
Get:1 https://repo.zabbix.com/zabbix/7.0/debian bookworm/main amd64 zabbix-agent2-plugin-mssql amd64 1:7.0.12-1+debian12 [2,412 kB]
Fetched 2,412 kB in 8s (293 kB/s)                                                                                                                                                                                 
Selecting previously unselected package zabbix-agent2-plugin-mssql.
(Reading database ... 37462 files and directories currently installed.)
Preparing to unpack .../zabbix-agent2-plugin-mssql_1%3a7.0.12-1+debian12_amd64.deb ...
Unpacking zabbix-agent2-plugin-mssql (1:7.0.12-1+debian12) ...
Setting up zabbix-agent2-plugin-mssql (1:7.0.12-1+debian12) ...
```

# List all installed packages
**Input**
```
sudo apt list --installed > all-packages-after-server-frontend-agent2.txt
```
**Output**
```
WARNING: apt does not have a stable CLI interface. Use with caution in scripts.
```

# List differences between 2 files
**Input**
```
diff -u all-packages-after-zabbix.txt all-packages-after-server-frontend-agent2.txt
```
**Output**
```
--- all-packages-after-zabbix.txt	2025-05-13 09:03:49.473399013 -0400
+++ all-packages-after-server-frontend-agent2.txt	2025-05-14 03:12:03.512484284 -0400
@@ -51,6 +51,11 @@
 file/stable,now 1:5.44-3 amd64 [installed]
 findutils/stable,now 4.9.0-4 amd64 [installed]
 firmware-linux-free/stable,now 20200122-1 all [installed,automatic]
+fontconfig-config/stable,now 2.14.1-4 amd64 [installed,automatic]
+fonts-dejavu-core/stable,now 2.37-6 all [installed,automatic]
+fonts-dejavu-extra/stable,now 2.37-6 all [installed,automatic]
+fonts-dejavu/stable,now 2.37-6 all [installed,automatic]
+fping/stable,now 5.1-1 amd64 [installed,automatic]
 fuse3/stable,now 3.14.0-4 amd64 [installed,automatic]
 gcc-12-base/stable,now 12.2.0-14 amd64 [installed]
 gettext-base/stable,now 0.21-12 amd64 [installed]
@@ -88,13 +93,16 @@
 krb5-locales/stable,stable-security,now 1.20.1-2+deb12u2 all [installed]
 laptop-detect/stable,now 0.16 all [installed]
 less/stable,stable-security,now 590-2.1~deb12u2 amd64 [installed]
+libabsl20220623/stable,now 20220623.1-1 amd64 [installed,automatic]
 libacl1/stable,now 2.3.1-3 amd64 [installed]
+libaom3/stable,stable-security,now 3.6.0-1+deb12u1 amd64 [installed,automatic]
 libapparmor1/stable,now 3.0.8-3 amd64 [installed]
 libapt-pkg6.0/stable,now 2.6.1 amd64 [installed]
 libargon2-1/stable,now 0~20171227-0.3+deb12u1 amd64 [installed]
 libattr1/stable,now 1:2.5.1-4 amd64 [installed]
 libaudit-common/stable,now 1:3.0.9-1 all [installed]
 libaudit1/stable,now 1:3.0.9-1 amd64 [installed]
+libavif15/stable,now 0.11.1-1 amd64 [installed,automatic]
 libblkid1/stable,now 2.38.1-5+deb12u3 amd64 [installed]
 libbpf1/stable,now 1:1.1.0-1 amd64 [installed]
 libbrotli1/stable,now 1.0.9-2+b6 amd64 [installed,automatic]
@@ -108,12 +116,19 @@
 libcap2/stable,now 1:2.66-4 amd64 [installed]
 libcbor0.8/stable,now 0.8.0-2+b1 amd64 [installed,automatic]
 libcom-err2/stable,now 1.47.0-2 amd64 [installed]
+libconfig-inifiles-perl/stable,now 3.000003-2 all [installed,automatic]
 libcrypt1/stable,now 1:4.4.33-2 amd64 [installed]
 libcryptsetup12/stable,now 2:2.6.1-4~deb12u2 amd64 [installed]
 libcurl3-gnutls/stable,now 7.88.1-10+deb12u12 amd64 [installed,automatic]
+libcurl4/stable,now 7.88.1-10+deb12u12 amd64 [installed,automatic]
+libdav1d6/stable,stable-security,now 1.0.0-2+deb12u1 amd64 [installed,automatic]
 libdb5.3/stable,now 5.3.28+dfsg2-1 amd64 [installed]
+libdbd-mariadb-perl/stable,now 1.22-1+b1 amd64 [installed,automatic]
+libdbi-perl/stable,now 1.643-4 amd64 [installed,automatic]
 libdbus-1-3/stable,now 1.14.10-1~deb12u1 amd64 [installed,automatic]
+libde265-0/stable,now 1.0.11-1+deb12u2 amd64 [installed,automatic]
 libdebconfclient0/stable,now 0.270 amd64 [installed]
+libdeflate0/stable,now 1.14-1 amd64 [installed,automatic]
 libdevmapper1.02.1/stable,now 2:1.02.185-2 amd64 [installed]
 libdiscover2/stable,now 2.1.2-10 amd64 [installed,automatic]
 libdrm-common/stable,now 2.4.114-1 all [installed,automatic]
@@ -122,17 +137,23 @@
 libefiboot1/stable,now 37-6 amd64 [installed,automatic]
 libefivar1/stable,now 37-6 amd64 [installed,automatic]
 libelf1/stable,now 0.188-2.1 amd64 [installed]
+libevent-core-2.1-7/stable,now 2.1.12-stable-8 amd64 [installed,automatic]
+libevent-extra-2.1-7/stable,now 2.1.12-stable-8 amd64 [installed,automatic]
+libevent-pthreads-2.1-7/stable,now 2.1.12-stable-8 amd64 [installed,automatic]
 libexpat1/stable,stable-security,now 2.5.0-1+deb12u1 amd64 [installed,automatic]
 libext2fs2/stable,now 1.47.0-2 amd64 [installed]
 libfdisk1/stable,now 2.38.1-5+deb12u3 amd64 [installed]
 libffi8/stable,now 3.4.4-1 amd64 [installed]
 libfido2-1/stable,now 1.12.0-2+b1 amd64 [installed,automatic]
+libfontconfig1/stable,now 2.14.1-4 amd64 [installed,automatic]
 libfreetype6/stable-security,now 2.12.1+dfsg-5+deb12u4 amd64 [installed,automatic]
 libfstrm0/stable,now 0.6.1-1 amd64 [installed,automatic]
 libfuse2/stable,now 2.9.9-6+b1 amd64 [installed,automatic]
 libfuse3-3/stable,now 3.14.0-4 amd64 [installed,automatic]
+libgav1-1/stable,now 0.18.0-1+b1 amd64 [installed,automatic]
 libgcc-s1/stable,now 12.2.0-14 amd64 [installed]
 libgcrypt20/stable,now 1.10.1-3 amd64 [installed]
+libgd3/stable,now 2.3.3-9 amd64 [installed,automatic]
 libgdbm-compat4/stable,now 1.23-3 amd64 [installed,automatic]
 libgdbm6/stable,now 1.23-3 amd64 [installed,automatic]
 libglib2.0-0/stable,now 2.74.6-2+deb12u5 amd64 [installed,automatic]
@@ -140,13 +161,17 @@
 libgmp10/stable,now 2:6.2.1+dfsg1-1.1 amd64 [installed]
 libgnutls30/stable,stable-security,now 3.7.9-2+deb12u4 amd64 [installed]
 libgpg-error0/stable,now 1.46-1 amd64 [installed]
+libgpm2/stable,now 1.20.7-10+b1 amd64 [installed,automatic]
 libgssapi-krb5-2/stable,stable-security,now 1.20.1-2+deb12u2 amd64 [installed]
+libheif1/stable,stable-security,now 1.15.1-1+deb12u1 amd64 [installed,automatic]
 libhogweed6/stable,now 3.8.1-2 amd64 [installed]
 libicu72/stable,now 72.1-3 amd64 [installed,automatic]
 libidn2-0/stable,now 2.3.3-1+b1 amd64 [installed]
 libip4tc2/stable,now 1.8.9-2 amd64 [installed]
 libjansson4/stable,now 2.14-2 amd64 [installed]
+libjbig0/stable,now 2.1-6.1 amd64 [installed,automatic]
 libjemalloc2/stable,now 5.3.0-1 amd64 [installed,automatic]
+libjpeg62-turbo/stable,now 1:2.1.5-2 amd64 [installed,automatic]
 libjson-c5/stable,now 0.16-2 amd64 [installed]
 libk5crypto3/stable,stable-security,now 1.20.1-2+deb12u2 amd64 [installed]
 libkeyutils1/stable,now 1.6.3-2 amd64 [installed]
@@ -156,18 +181,22 @@
 libkrb5support0/stable,stable-security,now 1.20.1-2+deb12u2 amd64 [installed]
 libldap-2.5-0/stable,now 2.5.13+dfsg-5 amd64 [installed,automatic]
 libldap-common/stable,now 2.5.13+dfsg-5 all [installed,automatic]
+liblerc4/stable,now 4.0.0+ds-2 amd64 [installed,automatic]
 liblmdb0/stable,now 0.9.24-1 amd64 [installed,automatic]
 liblocale-gettext-perl/stable,now 1.07-5 amd64 [installed]
 liblockfile-bin/stable,now 1.17-1+b1 amd64 [installed]
+libltdl7/stable,now 2.4.7-7~deb12u1 amd64 [installed,automatic]
 liblz4-1/stable,now 1.9.4-1 amd64 [installed]
 liblzma5/stable-security,now 5.4.1-1 amd64 [installed]
 libmagic-mgc/stable,now 1:5.44-3 amd64 [installed,automatic]
 libmagic1/stable,now 1:5.44-3 amd64 [installed,automatic]
+libmariadb3/stable,now 1:10.11.11-0+deb12u1 amd64 [installed,automatic]
 libmaxminddb0/stable,now 1.7.1-1 amd64 [installed,automatic]
 libmd0/stable,now 1.0.4-2 amd64 [installed]
 libmnl0/stable,now 1.0.4-3 amd64 [installed]
 libmount1/stable,now 2.38.1-5+deb12u3 amd64 [installed]
 libmspack0/stable,now 0.11-1 amd64 [installed,automatic]
+libncurses6/stable,now 6.4-4 amd64 [installed,automatic]
 libncursesw6/stable,now 6.4-4 amd64 [installed]
 libnettle8/stable,now 3.8.1-2 amd64 [installed]
 libnewt0.52/stable,now 0.52.23-1+b1 amd64 [installed]
@@ -176,6 +205,10 @@
 libnghttp2-14/stable,now 1.52.0-1+deb12u2 amd64 [installed,automatic]
 libnsl2/stable,now 1.3.0-2 amd64 [installed,automatic]
 libnss-systemd/stable,now 252.36-1~deb12u1 amd64 [installed]
+libnuma1/stable,now 2.0.16-1 amd64 [installed,automatic]
+libodbc2/stable,now 2.3.11-2+deb12u1 amd64 [installed,automatic]
+libonig5/stable,now 6.9.8-1 amd64 [installed,automatic]
+libopenipmi0/stable,now 2.0.33-1+b1 amd64 [installed,automatic]
 libp11-kit0/stable,now 0.24.1-2 amd64 [installed]
 libpam-modules-bin/stable,now 1.5.2-6+deb12u1 amd64 [installed]
 libpam-modules/stable,now 1.5.2-6+deb12u1 amd64 [installed]
@@ -194,6 +227,7 @@
 libpython3-stdlib/stable,now 3.11.2-1+b1 amd64 [installed,automatic]
 libpython3.11-minimal/stable,now 3.11.2-6+deb12u5 amd64 [installed,automatic]
 libpython3.11-stdlib/stable,now 3.11.2-6+deb12u5 amd64 [installed,automatic]
+librav1e0/stable,now 0.5.1-6 amd64 [installed,automatic]
 libreadline8/stable,now 8.2-1.3 amd64 [installed]
 librtmp1/stable,now 2.4+20151223.gitfa8646d.1-2+b2 amd64 [installed,automatic]
 libsasl2-2/stable,now 2.1.28+dfsg-10 amd64 [installed,automatic]
@@ -203,20 +237,29 @@
 libselinux1/stable,now 3.4-1+b6 amd64 [installed]
 libsemanage-common/stable,now 3.4-1 all [installed]
 libsemanage2/stable,now 3.4-1+b5 amd64 [installed]
+libsensors-config/stable,now 1:3.6.0-7.1 all [installed,automatic]
+libsensors5/stable,now 1:3.6.0-7.1 amd64 [installed,automatic]
 libsepol2/stable,now 3.4-2.1 amd64 [installed]
 libslang2/stable,now 2.3.3-3 amd64 [installed]
 libsmartcols1/stable,now 2.38.1-5+deb12u3 amd64 [installed]
+libsnmp-base/stable,now 5.9.3+dfsg-2 all [installed,automatic]
+libsnmp40/stable,now 5.9.3+dfsg-2 amd64 [installed,automatic]
+libsodium23/stable,now 1.0.18-1 amd64 [installed,automatic]
 libsqlite3-0/stable,now 3.40.1-2+deb12u1 amd64 [installed,automatic]
 libss2/stable,now 1.47.0-2 amd64 [installed]
+libssh-4/stable,stable-security,now 0.10.6-0+deb12u1 amd64 [installed,automatic]
 libssh2-1/stable,now 1.10.0-3+b1 amd64 [installed,automatic]
 libssl3/stable,now 3.0.15-1~deb12u1 amd64 [installed]
 libstdc++6/stable,now 12.2.0-14 amd64 [installed]
+libsvtav1enc1/stable,now 1.4.1+dfsg-1 amd64 [installed,automatic]
 libsystemd-shared/stable,now 252.36-1~deb12u1 amd64 [installed]
 libsystemd0/stable,now 252.36-1~deb12u1 amd64 [installed]
 libtasn1-6/stable,stable-security,now 4.19.0-2+deb12u1 amd64 [installed]
+libterm-readkey-perl/stable,now 2.38-2+b1 amd64 [installed,automatic]
 libtext-charwidth-perl/stable,now 0.04-11 amd64 [installed]
 libtext-iconv-perl/stable,now 1.7-8 amd64 [installed]
 libtext-wrapi18n-perl/stable,now 0.06-10 all [installed]
+libtiff6/stable,now 4.5.0-6+deb12u2 amd64 [installed,automatic]
 libtinfo6/stable,now 6.4-4 amd64 [installed]
 libtirpc-common/stable,now 1.3.3+ds-1 all [installed]
 libtirpc3/stable,now 1.3.3+ds-1 amd64 [installed]
@@ -226,9 +269,11 @@
 libusb-1.0-0/stable,now 2:1.0.26-1 amd64 [installed,automatic]
 libuuid1/stable,now 2.38.1-5+deb12u3 amd64 [installed]
 libuv1/stable,stable-security,now 1.44.2-1+deb12u1 amd64 [installed,automatic]
+libwebp7/stable,stable-security,now 1.2.4-0.2+deb12u1 amd64 [installed,automatic]
 libwrap0/stable,now 7.6.q-32 amd64 [installed,automatic]
 libx11-6/stable,stable-security,now 2:1.8.4-2+deb12u2 amd64 [installed,automatic]
 libx11-data/stable,stable-security,now 2:1.8.4-2+deb12u2 all [installed,automatic]
+libx265-199/stable,now 3.5-2+b1 amd64 [installed,automatic]
 libxau6/stable,now 1:1.0.9-1 amd64 [installed,automatic]
 libxcb1/stable,now 1.15-1 amd64 [installed,automatic]
 libxdmcp6/stable,now 1:1.1.2-3 amd64 [installed,automatic]
@@ -237,9 +282,11 @@
 libxmlsec1-openssl/stable,now 1.2.37-2 amd64 [installed,automatic]
 libxmlsec1/stable,now 1.2.37-2 amd64 [installed,automatic]
 libxmuu1/stable,now 2:1.1.3-3 amd64 [installed,automatic]
+libxpm4/stable,stable-security,now 1:3.5.12-1.1+deb12u1 amd64 [installed,automatic]
 libxslt1.1/stable-security,now 1.1.35-1+deb12u1 amd64 [installed,automatic]
 libxtables12/stable,now 1.8.9-2 amd64 [installed]
 libxxhash0/stable,now 0.8.1-1 amd64 [installed]
+libyuv0/stable,now 0.0~git20230123.b2528b0-1 amd64 [installed,automatic]
 libzstd1/stable,now 1.5.4+dfsg2-5 amd64 [installed]
 linux-base/stable,now 4.9 all [installed,automatic]
 linux-image-6.1.0-32-amd64/stable,now 6.1.129-1 amd64 [installed,automatic]
@@ -254,10 +301,14 @@
 mailcap/stable,now 3.70+nmu1 all [installed,automatic]
 man-db/stable,now 2.11.2-2 amd64 [installed]
 manpages/stable,now 6.03-2 all [installed]
+mariadb-client-core/stable,now 1:10.11.11-0+deb12u1 amd64 [installed,automatic]
+mariadb-client/stable,now 1:10.11.11-0+deb12u1 amd64 [installed,automatic]
+mariadb-common/stable,now 1:10.11.11-0+deb12u1 all [installed,automatic]
 mawk/stable,now 1.3.4.20200120-3.1 amd64 [installed]
 media-types/stable,now 10.0.0 all [installed]
 mime-support/stable,now 3.66 all [installed]
 mount/stable,now 2.38.1-5+deb12u3 amd64 [installed]
+mysql-common/stable,now 5.8+1.1.0 all [installed,automatic]
 nano/stable,now 7.2-1+deb12u1 amd64 [installed]
 ncurses-base/stable,now 6.4-4 all [installed]
 ncurses-bin/stable,now 6.4-4 amd64 [installed]
@@ -265,6 +316,8 @@
 netbase/stable,now 6.4 all [installed]
 netcat-traditional/stable,now 1.10-47 amd64 [installed]
 nftables/stable,now 1.0.6-2+deb12u2 amd64 [installed]
+nginx-common/stable,now 1.22.1-9+deb12u1 all [installed,automatic]
+nginx/stable,now 1.22.1-9+deb12u1 amd64 [installed,automatic]
 open-vm-tools/stable,stable-security,now 2:12.2.0-1+deb12u2 amd64 [installed]
 openssh-client/stable,stable-security,now 1:9.2p1-2+deb12u5 amd64 [installed]
 openssh-server/stable,stable-security,now 1:9.2p1-2+deb12u5 amd64 [installed,automatic]
@@ -277,7 +330,29 @@
 perl-base/stable-security,now 5.36.0-7+deb12u2 amd64 [installed]
 perl-modules-5.36/stable-security,now 5.36.0-7+deb12u2 all [installed,automatic]
 perl/stable-security,now 5.36.0-7+deb12u2 amd64 [installed]
+php-bcmath/stable,now 2:8.2+93 all [installed,automatic]
+php-common/stable,now 2:93 all [installed,automatic]
+php-curl/stable,now 2:8.2+93 all [installed,automatic]
+php-fpm/stable,now 2:8.2+93 all [installed,automatic]
+php-gd/stable,now 2:8.2+93 all [installed,automatic]
+php-ldap/stable,now 2:8.2+93 all [installed,automatic]
+php-mbstring/stable,now 2:8.2+93 all [installed,automatic]
+php-mysql/stable,now 2:8.2+93 all [installed,automatic]
+php-xml/stable,now 2:8.2+93 all [installed,automatic]
+php8.2-bcmath/stable-security,now 8.2.28-1~deb12u1 amd64 [installed,automatic]
+php8.2-cli/stable-security,now 8.2.28-1~deb12u1 amd64 [installed,automatic]
+php8.2-common/stable-security,now 8.2.28-1~deb12u1 amd64 [installed,automatic]
+php8.2-curl/stable-security,now 8.2.28-1~deb12u1 amd64 [installed,automatic]
+php8.2-fpm/stable-security,now 8.2.28-1~deb12u1 amd64 [installed,automatic]
+php8.2-gd/stable-security,now 8.2.28-1~deb12u1 amd64 [installed,automatic]
+php8.2-ldap/stable-security,now 8.2.28-1~deb12u1 amd64 [installed,automatic]
+php8.2-mbstring/stable-security,now 8.2.28-1~deb12u1 amd64 [installed,automatic]
+php8.2-mysql/stable-security,now 8.2.28-1~deb12u1 amd64 [installed,automatic]
+php8.2-opcache/stable-security,now 8.2.28-1~deb12u1 amd64 [installed,automatic]
+php8.2-readline/stable-security,now 8.2.28-1~deb12u1 amd64 [installed,automatic]
+php8.2-xml/stable-security,now 8.2.28-1~deb12u1 amd64 [installed,automatic]
 procps/stable,now 2:4.0.2-3 amd64 [installed]
+psmisc/stable,now 23.6-1 amd64 [installed,automatic]
 publicsuffix/stable,now 20230209.2326-1 all [installed,automatic]
 python-apt-common/stable,now 2.6.0 all [installed,automatic]
 python3-apt/stable,now 2.6.0 amd64 [installed,automatic]
@@ -307,6 +382,7 @@
 sed/stable,now 4.9-1 amd64 [installed]
 sensible-utils/stable,now 0.0.17+nmu1 all [installed]
 shared-mime-info/stable,now 2.2-1 amd64 [installed,automatic]
+snmpd/stable,now 5.9.3+dfsg-2 amd64 [installed,automatic]
 sudo/stable,now 1.9.13p3-1+deb12u1 amd64 [installed]
 systemd-sysv/stable,now 252.36-1~deb12u1 amd64 [installed]
 systemd-timesyncd/stable,now 252.36-1~deb12u1 amd64 [installed]
@@ -335,7 +411,13 @@
 xdg-user-dirs/stable,now 0.18-1 amd64 [installed,automatic]
 xkb-data/stable,now 2.35.1-1 all [installed,automatic]
 xz-utils/stable-security,now 5.4.1-1 amd64 [installed]
-zabbix-release/now 1:7.0-2+debian12 all [installed,local]
+zabbix-agent2-plugin-mssql/zabbix,now 1:7.0.12-1+debian12 amd64 [installed]
+zabbix-agent2/zabbix,now 1:7.0.12-1+debian12 amd64 [installed]
+zabbix-frontend-php/zabbix,now 1:7.0.12-1+debian12 all [installed]
+zabbix-nginx-conf/zabbix,now 1:7.0.12-1+debian12 all [installed]
+zabbix-release/zabbix,zabbix,now 1:7.0-2+debian12 all [installed]
+zabbix-server-mysql/zabbix,now 1:7.0.12-1+debian12 amd64 [installed]
+zabbix-sql-scripts/zabbix,now 1:7.0.12-1+debian12 all [installed]
 zerofree/stable,now 1.1.1-1 amd64 [installed,automatic]
 zlib1g/stable,now 1:1.2.13.dfsg-1 amd64 [installed]
 zstd/stable,now 1.5.4+dfsg2-5 amd64 [installed]
```



sudo apt -y install default-mysql-server
sudo systemctl status mysql










# Download requirements packages
## Frontend
```
sudo apt -y install default-mysql-server
sudo apt -y install php8.2
sudo apt -y install nginx
sudo apt -y install php8.2-mysql
sudo apt -y install php-bcmath
sudo apt -y install php-mbstring
sudo apt -y install php-net-socket
sudo apt -y install php-gd

# If necessary
sudo apt -y install php-xml
sudo apt -y install php-xmlwriter
sudo apt -y install php-xmlreader
sudo apt -y install php-ctype

sudo apt -y install php-session
```
## Server/proxy
```
sudo apt -y install libpcre2-32-0
sudo apt -y install libevent-2.1-7
sudo apt -y install libevent-pthreads-2.1-7
...
```




# Create initial database
**Input**
```
mysql -uroot -p
```
**Output**
```
Enter password: 
ERROR 2002 (HY000): Can't connect to local server through socket '/run/mysqld/mysqld.sock' (2)
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
