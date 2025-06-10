# Set up Glpi for admin notifications

sudo apt update

sudo apt -y install apache2 php8.2 php8.2-curl php8.2-zip php8.2-gd php8.2-intl php8.2-intl php-pear php8.2-imagick php8.2-imap php-memcache php8.2-pspell php8.2-tidy php8.2-xmlrpc php8.2-xsl php8.2-mbstring php8.2-ldap php8.2-ldap php-cas php-apcu libapache2-mod-php8.2 php8.2-mysql php-bz2 wget php8.2-gd

sudo systemctl status apache2

sudo apt install -y mariadb-server mariadb-client

sudo systemctl status mariadb

sudo mysql_secure_installation

sudo mysql -u root -p

CREATE DATABASE mon1;

GRANT ALL PRIVILEGES ON mon1.* TO 'mon1'@'localhost' IDENTIFIED BY 'password';

FLUSH PRIVILEGES;

EXIT;

export VER="10.0.17"

wget https://github.com/glpi-project/glpi/releases/download/$VER/glpi-$VER.tgz

tar xvf glpi-$VER.tgz

sudo mv glpi /var/www/html/

sudo chown -R www-data:www-data /var/www/html/glpi

