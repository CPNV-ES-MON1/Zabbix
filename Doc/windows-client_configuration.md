# installation de l'agent
```
on installe l'agent sur la documentation de zabbix
https://www.zabbix.com/download_agents
```
# configuration de l'agent**

**spécification du dossier d'installation**
```
on lance le wizard et après avoir accepté les thermes de la licence, on spcécifie où sera installer les fichiers de confuguration de l'agent.
![image](https://github.com/user-attachments/assets/268fe30f-22a9-4586-8204-388349f27d1a)
on laisse les paramètres par défault
```
**configuration du server zabbix**
```
dans la page suivane on va  spécifié le hostname de notre client, l'ip du server et le port d'écoute de  l'agent et aussi le server de check actif
![image](https://github.com/user-attachments/assets/f40bb979-c99f-4b98-b748-c370a66bda7c)

on met donc l'ip du server et le hostname de notre client et on laisse le port par default
on laisse lôption du server active en 127.0.0.1 car on ne vas pas utiliser de template qui sont en mode actif pour cette machine.
le enable PSK permet de faire du chiffrement simétrique que nous n'allons pas utiliser ici
finalement le add agent location to the PATH permet de faire une variable d'environement windows pour pouvoir gérer l'agent avec des terminaux
```
# configuration sur zabbix

## ajout de l'host
```
monitoring>host on va cliquer sur "create host"
```
**onglet host**
```
dans l'onglet host on va lui mettre sont hostname zabbix, sont visible name.
ensuite sous template on choisis le template "windows by zabbix agent"
!!ATTENTION!!
il existe un "windows by zabbix agent active" il ne faut pas le prendre car le server actif de l'agent n'a pas été configuré

on le met ensuite dans un Host Groupe (exemple ci dessous avec le groupe "irtual machine")

on lui aoute une interface qui est le moen de communication entre le zabbix server et l'agent et choisisant le type agent.
dans l'insterface on spécifie l'IP et le port qu'utilise l'agent.
![image](https://github.com/user-attachments/assets/ec6cc0c8-4c1b-431b-870a-2b888349e91d)
```
**onglet inventory**
```
ici on passe l'inventaire en mode automatique sinon on ne pourra pas avoir cet host sous inventory>host dans zabbix
```
## configuration de l'item
```
sous Data collection>hosts on clique sur items de notre host.

ici on cherche l'item "CPU utilisation" et on clique dessus.

là on modifie l'option "Update interval" pour mettre 10s
```
