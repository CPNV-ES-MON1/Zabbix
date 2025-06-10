Mon 1 - Zabbix documentation <br>
ICT - GGR - RDI

# Ticketing - GLPI

## Table of content
- [1. Set up Glpi for admin notifications](#1-set-up-glpi-for-admin-notifications)
- [2. on glpi server](#2-on-glpi-server)
- [3. on the zabbix webui](#3-on-the-zabbix-webui)
  - [3.1. media type creation](#31-media-type-creation)
  - [3.2. user creation](#32-user-creation)
  - [3.3. add trigger action](#33-add-trigger-action)
    - [3.3.1. operations](#331-operations)
    - [3.3.2. Update operations](#332-update-operations)

<br>
<br>
<br>

# 1. Set up Glpi for admin notifications

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

# 2. on glpi server
Setup>General>API
```
URL of the API = http://129.168.153.147/glpi/apirest.php/Ticket
Enable Rest API = yes
Enable login with credentials = yes
Enable login with external token = yes
```
under the same page add an API client
```
Name = <name>
Active = yes
IPv4 Address Range Start = <Zabbix server ip>
IPv4 Address Range End = <Zabbix server ip>
Application Token (app_token) = Regenerate
```
add the client
go to the api client tab and copy the Application Token (app_token).
then run this coommand with the token and a user login and password
```
curl -X POST http://[IP_GLPI]/glpi/apirest.php/initSession   -H "Content-Type: application/json"   -H "App-Token: [TOKEN]"   -d '{
    "login": "[LOGIN]",
    "password": "[password]"
  }'
```
copy the session token that you get.
go to usr/lib/zabbix/alertscripts and create a script "ticket.sh"
```
sudo touch ticket.sh
sudo chmod +x ticket.sh
```
pasre the following script in it
```
#!/bin/bash

# Variables
GLPI_URL="http://192.168.153.147/glpi/apirest.php"
APP_TOKEN="020Iz4mpzZKA1Evmi1kEKffUERNCtRzXnqznrmZu"
SESSION_TOKEN="583ipdd817jkklq0ctsvesa867"
SCRIPT_DIR="/usr/lib/zabbix/alertscripts"

# Créer le ticket et récupérer l'ID
response=$(curl -s -X POST "$GLPI_URL/Ticket" \
  -H "Content-Type: application/json" \
  -H "App-Token: $APP_TOKEN" \
  -H "Session-Token: $SESSION_TOKEN" \
  -d '{
        "input": {
          "name": "Alerte Zabbix : client windows CPU stress",
          "content": "le client windows a son cpu au dessus de 90%",
          "status": 1,
          "priority": 3
        }
      }')

# Debug : afficher la réponse complète
echo "Réponse GLPI : $response"

# Extraire l'ID
ticket_id=$(echo "$response" | grep -oP '"id"\s*:\s*\K\d+')

# Sauvegarder dans un fichier
if [[ -n "$ticket_id" ]]; then
  echo "$ticket_id" > "$SCRIPT_DIR/ticket_id.txt"
  echo "Ticket créé avec ID : $ticket_id (enregistré dans $SCRIPT_DIR/ticket_id.txt)"
else
  echo "Erreur : impossible d'extraire l'ID du ticket."
fi

```
create the file ticket_id.txt
```
sudo touch ticket_id.txt
sudo chmod 666 ticket_id.txt
```
create a second script 
```
sudo touch delticket.sh
sudo chmod +x delticket.sh
```
parse the following code in it
```
  GNU nano 7.2                                                                                                     delticket.sh                                                                                                              #!/bin/bash

GLPI_URL="http://192.168.153.147/glpi/apirest.php"
APP_TOKEN="020Iz4mpzZKA1Evmi1kEKffUERNCtRzXnqznrmZu"
SESSION_TOKEN="583ipdd817jkklq0ctsvesa867"
TICKET_ID_FILE="/usr/lib/zabbix/alertscripts/ticket_id.txt"

if [[ ! -f "$TICKET_ID_FILE" ]]; then
  echo "Erreur : le fichier $TICKET_ID_FILE est introuvable."
  exit 1
fi

ticket_id=$(cat "$TICKET_ID_FILE")

if [[ -z "$ticket_id" || ! "$ticket_id" =~ ^[0-9]+$ ]]; then
  echo "Erreur : ID invalide dans le fichier ($ticket_id)."
  exit 1
fi

echo "Suppression du ticket ID $ticket_id..."
response=$(curl -s -X DELETE "$GLPI_URL/Ticket/$ticket_id" \
  -H "App-Token: $APP_TOKEN" \
  -H "Session-Token: $SESSION_TOKEN")

# Vérifier si la suppression a réussi même si la réponse n'est pas vide
if echo "$response" | grep -q "\"$ticket_id\":true"; then
  echo "✅ Ticket $ticket_id supprimé avec succès."
else
  echo "❌ Échec de la suppression. Réponse de l'API :"
  echo "$response"
fi
```
# 3. on the zabbix webui
## 3.1. media type creation
go to alertes>media types
enable glpi
```
Name = GLPI
Type = Script
Script name = ticket.sh

```
clone the media
```
Name = GLPi-closeTicket
Type = Script
Script name = delticket.sh
```
## 3.2. user creation
go to Users>Users>create user
```
Username = glpi
Name = glpi
Last name = glpi
Groups = Zabbix administrators

```
add 2 medias
```
type =GLPI
Send to =http://192.168.153.147/glpi
When active =1-7,00:00-24:00
----------------
type =GLPI-closeTicket
Send to =http://192.168.153.147/glpi
When active =1-7,00:00-24:00
```
## 3.3. add trigger action
on alerts>actions>trigger action
enable the 	Report problems to Zabbix administrators
### 3.3.1. operations
```
Send to users = glpi
Send to media type = GLPI
```
### 3.3.2. Update operations
```
Send to users = glpi
Send to media type = GLPI-closeTicket
```
