# installation de l'agent
```
on installe l'agent sur la documentation de zabbix
https://www.zabbix.com/download_agents
```
# configuration de l'agent**

**spécification du dossier d'installation**
```
on lance le wizard et après avoir accepté les thermes de la licence, on spcécifie où sera installer les fichiers de confuguration de l'agent.
on laisse les paramètres par défault
```
**configuration du server zabbix**
```
dans la page suivane on va  spécifié le hostname de notre client, l'ip du server et le port d'écoute de  l'agent et aussi le server de check actif


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

ici on va ajouter notre client windows sous "Hosts" et on va ajouter une collonne avec l'item "CPU utilization"
```

