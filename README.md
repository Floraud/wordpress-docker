# Présentation
L'objectif est d'avoir une infrastructure minimaliste disposant du maximum de containers wordpress et mariadb derrière un container reverse-proxy pour exposer des sites en https à moindre coût.
Cette infrastructure doit répondre au principe de l'IaC et être facilement redéployable, pour migrer (on premise par exemple) ou en cas d'incident, avec le moins d'actions manuelles possibles.

# To-do list
- [ ] Préremmplir l'installation wordpress avec wordpress cli ou en alimentant en amont avec un fichier .php
- [ ] Gestion des secrets
- [ ] Certbot
- [ ] Sécurité ?
	- [ ] fail2ban sur le ssh de l'host ? = ansible et en profiter au début pour installer docker et uploader le fichier ?
	- [ ] Qu'existe-t-il au niveau du container ?
- [ ] comment assurer les backups et sauvegardes ?
- [ ] Comment assurer upgrade

# Architecture
## Diagramme
```mermaid
graph TD;

user---traefik;

subgraph container
	traefik---wordpress_1;
	traefik---wordpress_2;

	subgraph docker_network_2
		wordpress_2---mariadb_2;
	end

	subgraph docker_network_1
		wordpress_1---mariadb_1;
	end
end
    
```

## Network flow Matrix
***WIP***

|Source|Destination  |Port          |Flux          |
|------|-------------|--------------|--------------|
|any   |server       |tcp/80 tcp/443|accès web     |
|any   |server       |tcp/22        |administration|
|server|any          |tcp/80 tcp/443|repo update   |
|server|1.1.1.1 other|tcp/53 udp/53 |DNS           |
|server|timesrv1 et 2|udp/123       |NTP           |


## Images docker
- **Explicitez le numéro de version**

### Reverse Proxy
- Traefik > nginx car le renouvellement de certificat est pensé dans Traefik et que c'est une solution bien adaptée aux containers.

## Questionnement
- MariaDB en container ? **OK pour  site simple à faible criticité pour optimiser coût**
	- **Pros**
		- Une Mariadb par appli recommandé
		- Facilite le redéploiement
	- **Cons**
		- Pas pour de la grosse infra
		- Demande de gérer la partie volume

## Hébergement
- **VPS**
	- **Pulseheberg**
	- **~~OVH~~**
		- 1,10 € en plus par mois pour les snapshots
		- Moins de puissance de calcul pour plus cher que Pulseheberg
- **~~On premise~~**
	- Demande à minima Proxmox (cluster ?) avec Opnsense devant
	- Nécessite calcul prix éléctricité au kwh en plus de l'équipement

# Administration
## Déploiement
- Lancez le déploiement des containers à l'aide de `sudo docker-compose up -d`

## Process d'Upgrade
**A tester** :
- Modifiez le fichier docker-compose.yml avec les nouvelles versions (tous les trimestres)
- pour arrêter les anciens containers et en créer de nouveaux :  `sudo docker-compose down && sudo docker-compose up -d`

## Process de Backup
- Ne concerne que les volumes

## Process de Restauration

<details><summary>

# Monitoring
</summary>

- `docker stats` : pour savoir où on en est en terme de ressources. 
- ~~Zabbix~~ : trop gourmand car nécessite une BDD en plus.
- ~~Prometheus~~ : Consomme aussi beaucoup de ressources d'après les retours
- ~~Cadvisor~~ : demande d'être là en live.

</details>

# Troubleshooting
- Voir les logs d'un container : `sudo docker logs <name>`
- Contrôler les containers toujours présents : `sudo docker ps`
- Contrôler les volumes toujours présents : `sudo docker volume ls`
- Relancer les containers : `sudo docker-compose up -d`
- **Attention !** Cette commande est faite pour tout supprimer et recréer, containers comme volumes : `sudo docker-compose down --remove-orphans && sudo docker volume prune -f && sudo docker network prune -f && sudo docker compose up`

# Sources et ressources
- [nginx.conf référencé par le docker hub de wordpress](https://gist.github.com/md5/d9206eacb5a0ff5d6be0)