# Présentation
L'objectif est d'avoir une infrastructure minimaliste disposant du maximum de containers wordpress et mariadb derrière un container nginx pour exposer des sites en https à moindre coût.
Cette infrastructure doit répondre au principe de l'IaC est être facilement redéployable pour migrer (on premise par exemple) ou en cas de problème avec le moins d'actions manuelles possibles.

# To-do list
- [ ]  Gestion des secrets
- [ ]  Sites multiples basé sur nginx mais compartimentés
	- [Exemple de comment séparer les réseaux](https://www.reddit.com/r/docker/comments/tvvdrd/multiple_mariadbmysql_containers_on_the_same/?rdt=54580)
- [ ] Certbot (nécessite vps et domaine)
- [ ] Sites multiples

# Architecture
## Diagram
*wip*

## Network flow Matrix
*wip*

## Images docker
- **Les alpines sont plus légères et donc à prioriser**
- **Explicitez le numéro de version**

### wordpress
[doc avec les variances](https://hub.docker.com/_/wordpress)
> This variant contains PHP-FPM, which is a FastCGI implementation for PHP. See the PHP-FPM website for more information about PHP-FPM.

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

## Logging

## Troubleshooting
- Voir les logs d'un container : `sudo docker logs <name>`
- Contrôler les containers toujours présents : `sudo docker ps`
- Contrôler les volumes toujours présents : `sudo docker volume ls`
- Relancer les containers : `sudo docker-compose up -d`

# Exploitation Wordpress

# Incidents

# Sources et ressources
- [nginx.conf référencé par le docker hub de wordpress](https://gist.github.com/md5/d9206eacb5a0ff5d6be0)