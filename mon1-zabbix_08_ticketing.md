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

```
sudo apt update
```
```
sudo apt -y install apache2 php8.2 php8.2-curl php8.2-zip php8.2-gd php8.2-intl php8.2-intl php-pear php8.2-imagick php8.2-imap php-memcache php8.2-pspell php8.2-tidy php8.2-xmlrpc php8.2-xsl php8.2-mbstring php8.2-ldap php8.2-ldap php-cas php-apcu libapache2-mod-php8.2 php8.2-mysql php-bz2 wget php8.2-gd
```
```
sudo systemctl status apache2
```
```
sudo apt install -y mariadb-server mariadb-client
```
```
sudo systemctl status mariadb
```
```
sudo mysql_secure_installation
```
connect with root accout say yes to all and when asked to, change to root password
```
sudo mysql -u root -p
```
```
CREATE DATABASE mon1;
GRANT ALL PRIVILEGES ON mon1.* TO 'mon1'@'localhost' IDENTIFIED BY 'password';
FLUSH PRIVILEGES;
EXIT;
```
```
export VER="10.0.17"
```
```
wget https://github.com/glpi-project/glpi/releases/download/$VER/glpi-$VER.tgz
```
```
tar xvf glpi-$VER.tgz
```
```
sudo mv glpi /var/www/html/
```
```
sudo chown -R www-data:www-data /var/www/html/glpi
```

# 2. On glpi server

http://"ip glpi server":8080/glpi/install/install.php

## Database connection setup
SQL server (MariaDB or MySQL)
```
localhost
```
SQL User
```
mon1
```
SQL Password
```
"nice password"
```

## Test of the connection at the database
- Select db name: mon1

## Login
- user: glpi
- password: glpi (default username for glpi)

## Setup on Webui
Setup>General>API
```
URL of the API = http://<ip glpi server>:8080/glpi/apirest.php/Ticket
Enable Rest API = yes
Enable login with credentials = yes
Enable login with external token = yes
```
Under the same page add an API client
```
Name = <zabbix-server>
Active = yes
IPv4 Address Range Start = <Zabbix server ip>
IPv4 Address Range End = <Zabbix server ip>
Regenerate: true
```

- Go to Setup>General>API
- Click on zabbix-server
- Copy Application Token (app_token)

# On Zabbix server
- Run this commmand with
  - ip of glpi server
  - the token
  - add glpi login and password
- 
```
curl -X POST http://<ip glpi server>/glpi/apirest.php/initSession   -H "Content-Type: application/json"   -H "App-Token: <app_token>"   -d '{
    "login": "<glpi>",
    "password": "<glpi>"
  }'
```

- Copy the "session token" that you get!
```
cd /usr/lib/zabbix/alertscripts/
sudo touch ticket.sh
sudo chmod +x ticket.sh
sudo nano ticket.sh
```

- Paste the following script in this file
- Modifiy the "ip glpi server"
- Modify the "app_token" with the previous received
- Modify the "session token" with the previous received
```
#!/bin/bash

# Variables
GLPI_URL="http://<ip glpi server>/glpi/apirest.php"
APP_TOKEN="<app_token>"
SESSION_TOKEN="<session token>"
SCRIPT_DIR="/usr/lib/zabbix/alertscripts"

# Créer le ticket et récupérer l'ID
response=$(curl -s -X POST "$GLPI_URL/Ticket" \
  -H "Content-Type: application/json" \
  -H "App-Token: $APP_TOKEN" \
  -H "Session-Token: $SESSION_TOKEN" \
  -d '{
        "input": {
          "name": "Alerte Zabbix : client windows CPU stress",
          "content": "Le client Windows a son CPU au-dessus de 90%",
          "status": 1,
          "priority": 3
        }
      }')

# Debug : afficher la réponse complète
echo "Réponse complète de l'API : $response"

# Extraire l'ID du ticket
ticket_id=$(echo "$response" | jq -r '.id')

# Sauvegarder dans un fichier
if [[ -n "$ticket_id" && "$ticket_id" =~ ^[0-9]+$ ]]; then
  echo "$ticket_id" > "$SCRIPT_DIR/ticket_id.txt"
  echo "✅ Ticket créé avec ID : $ticket_id (enregistré dans $SCRIPT_DIR/ticket_id.txt)"
else
  echo "❌ Erreur : impossible d'extraire l'ID du ticket."
  echo "Réponse de l'API : $response"
fi
```

- create a file nammed ticket_id.txt
```
sudo touch ticket_id.txt
sudo chmod 666 ticket_id.txt
```

create a second script nammed delticket.sh
```
sudo touch delticket.sh
sudo chmod +x delticket.sh
sudo nano delticket.sh
```

- Paste the following script in file delticket.sh
- Modifiy the "ip glpi server"
- Modify the "app_token" with the previous received
- Modify the "session token" with the previous received
```
#!/bin/bash

# Variables
GLPI_URL="http://<ip glpi server>/glpi/apirest.php"
APP_TOKEN="<app_token>"
SESSION_TOKEN="<session token>"
TICKET_ID_FILE="/usr/lib/zabbix/alertscripts/ticket_id.txt"

# Vérifier si le fichier contenant l'ID du ticket existe
if [ ! -f "$TICKET_ID_FILE" ]; then
  echo "Erreur : le fichier $TICKET_ID_FILE est introuvable."
  exit 1
fi

# Lire l'ID du ticket depuis le fichier
ticket_id=$(cat "$TICKET_ID_FILE")

# Vérifier si l'ID est valide
if [ -z "$ticket_id" ] || ! echo "$ticket_id" | grep -qE '^[0-9]+$'; then
  echo "Erreur : ID invalide dans le fichier ($ticket_id)."
  exit 1
fi

# Créer le corps de la requête PUT
json_payload=$(jq -n --arg id "$ticket_id" '{input: [{"id": $id, "status": 6}]}')

# Envoyer la requête PUT à l'API REST de GLPI
response=$(curl -s -X PUT "$GLPI_URL/Ticket" \
  -H "Content-Type: application/json" \
  -H "App-Token: $APP_TOKEN" \
  -H "Session-Token: $SESSION_TOKEN" \
  -d "$json_payload")

# Vérifier la réponse de l'API
if echo "$response" | grep -q "\"$ticket_id\":true"; then
  echo "✅ Ticket $ticket_id clôturé avec succès."
else
  echo "❌ Échec de la clôture. Réponse de l'API :"
  echo "$response"
fi
```

# 3. On the Zabbix Webui
## 3.1. Media type creation
Alerts>Media types>enable GLPI>modify GLPI
```
Name = GLPi
Type = Script
Script name = ticket.sh
```
- Update
- Clone
```
Name = GLPi-closeTicket
Type = Script
Script name = delticket.sh
```
- Add

## 3.2. User creation
Users>Users>Create user
/User
```
Username = glpi
Groups = Zabbix administrators
Password = <a beautiful password>
```

/Media
- Media: Add
```
type = GLPI
Send to =http://<ip glpi server>:8080/glpi
When active =1-7,00:00-24:00

type = GLPI-closeTicket
Send to = http://<ip glpi server>/glpi
When active =1-7,00:00-24:00
```

/Permissions
- Add: Super admin role
- Add

## 3.3. Add trigger action

Alerts>Actions>Trigger actions

Open "Report problems to Zabbix administrators"
### Action
```
Enabled: true
```

### 3.3.1. Operations
- Operations: Remove all existing
- Operations: Add
```
Send to users = glpi
Send to media type = GLPI
```
- Add

### 3.3.2. Recovery operations
- Recovery operations: Remove all existing
- Recovery operations: Add
```
Send to users = glpi
Send to media type = GLPI-closeTicket
```
- Add
- Update

### Uncheck unused tick
- Pause operations for symptom problems
- Pause operations for suppressed problems
