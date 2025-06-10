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

# on glpi server
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
GLPI_URL="http://[IP_GLPI]/glpi/apirest.php"
APP_TOKEN="[API_USER_TOKEN]"
SESSION_TOKEN="CURL_RESULT_TOKEN"


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

# Extraire l'ID
ticket_id=$(echo "$response" | grep -oP '"id"\s*:\s*\K\d+')


if [[ -n "$ticket_id" ]]; then
  echo "$ticket_id" > ticket_id.txt
  echo "Ticket créé avec ID : $ticket_id (enregistré dans ticket_id.txt)"
else
  echo "Erreur : impossible d'extraire l'ID du ticket."
  echo "Réponse complète : $response"
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
#!/bin/bash

GLPI_URL="http://[IP_GLPI]/glpi/apirest.php"
APP_TOKEN="[USER_TOKEN]"
SESSION_TOKEN="[CURL_RESULT_TOKEN]"
TICKET_ID_FILE="./ticket_id.txt"

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
# on the zabbix webui
## media type creation
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
## user creation
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
## add trigger action
on alerts>actions>trigger action
enable the 	Report problems to Zabbix administrators
### operations
```
Send to users = glpi
Send to media type = GLPI
```
### Update operations
```
Send to users = glpi
Send to media type = GLPI-closeTicket
```
