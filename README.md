# Présentation
L'objectif est d'avoir une infrastructure minimaliste disposant du maximum de containers wordpress et mariadb derrière un container reverse-proxy pour exposer des sites en https à moindre coût.
Cette infrastructure doit répondre au principe de l'IaC est être facilement redéployable pour migrer (on premise par exemple) ou en cas de problème avec le moins d'actions manuelles possibles.

# To-do list
- [ ] Gestion des secrets
- [ ] Certbot (nécessite vps et domaine)
- [ ] Comment assurer le monitoring, les backups et sauvegardes ?
- [ ] Est-ce possible de pré-remplir l'installation du WordPress ?

# Architecture
## Diagram
```mermaid
graph TD;

linkStyle default interpolate basis;

user-->traefik;

subgraph container
	traefik-->wordpress_1;
	traefik-->wordpress_2;

	subgraph docker_network_2
		wordpress_2-->mariadb_2;
	end

	subgraph docker_network_1
		wordpress_1-->mariadb_1;
	end
end
    
```

## Network flow Matrix
*wip*

## Images docker
- **Les alpines sont plus légères et donc à prioriser**
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

## Monitoring

## Troubleshooting
- Voir les logs d'un container : `sudo docker logs <name>`
- Contrôler les containers toujours présents : `sudo docker ps`
- Contrôler les volumes toujours présents : `sudo docker volume ls`
- Relancer les containers : `sudo docker-compose up -d`
- **Attention !** Cette commande est faite pour tout supprimer et recréer, containers comme volumes : `sudo docker-compose down --remove-orphans && sudo docker volume prune -f && sudo docker compose up`

# Sources et ressources
- [nginx.conf référencé par le docker hub de wordpress](https://gist.github.com/md5/d9206eacb5a0ff5d6be0)