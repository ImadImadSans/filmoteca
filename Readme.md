# Filmoteca  

Filmoteca est une application de gestion de films développée dans le cadre de la formation INF5. Ce projet suit l'architecture MVC et utilise Docker pour simplifier son déploiement.

## Prérequis  

Avant de commencer, assurez-vous d'avoir les outils suivants installés sur votre machine :  
1. **Docker** : pour exécuter les conteneurs. [Télécharger Docker](https://www.docker.com/)  
2. **Docker Compose** : inclus avec Docker Desktop.  
3. **Make** : pour exécuter les commandes prédéfinies.  
   - Si vous utilisez VS Code, vous pouvez installer l’extension [Makefile Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.makefile-tools) pour faciliter l’utilisation de `Make`.  

---

## Commandes Make  

Voici les commandes Make disponibles pour gérer le projet :  

```make
# Chemin vers le fichier docker-compose
DOCKER_COMPOSE_FILEPATH=.docker/docker-compose.yaml
DOCKER_PHP_CONTAINER=filmoteca_php

# Démarrer les conteneurs en arrière-plan
up:
	docker compose -f $(DOCKER_COMPOSE_FILEPATH) up -d

# Arrêter les conteneurs
down:
	docker compose -f $(DOCKER_COMPOSE_FILEPATH) down

# Recréer les conteneurs (utile après modification des Dockerfiles)
build:
	docker compose -f $(DOCKER_COMPOSE_FILEPATH) up --build -d

# Voir les logs des conteneurs
logs:
	docker compose -f $(DOCKER_COMPOSE_FILEPATH) logs -f

# Vérifier le statut des conteneurs
status:
	docker compose -f $(DOCKER_COMPOSE_FILEPATH) ps

# Nettoyer les conteneurs, réseaux, volumes, et images créés par `up`
clean:
	docker compose -f $(DOCKER_COMPOSE_FILEPATH) down --volumes --remove-orphans

# Redémarrer les conteneurs
restart: down up

# Se connecter au conteneur PHP
start:
	docker exec -it $(DOCKER_PHP_CONTAINER) /bin/bash

# Exécuter `composer install`
composer-install:
	docker exec $(DOCKER_PHP_CONTAINER) composer install

# Regénérer l'autoloading de Composer
dump-autoload:
	docker exec $(DOCKER_PHP_CONTAINER) composer dump-autoload
```

## Utilisation

1. **Cloner le projet** :  
   ```bash  
   git clone https://github.com/yhammououali/filmoteca.git  
   cd filmoteca  
   ```  

2. **Configurer le fichier `/etc/hosts` (ou équivalent)** :  
   Ajoutez la ligne suivante pour configurer le VirtualHost :  
   ```  
   127.0.0.1 filmoteca.local  
   ```   

3. **Démarrer le projet** :  
   ```bash  
   make up  
   ```  

4. **Construire le projet après une modification des fichiers Docker** :  
   ```bash  
   make build  
   ```  

5. **Vérifier le statut des conteneurs** :  
   ```bash  
   make status  
   ```  

6. **Se connecter au conteneur PHP** :  
   ```bash  
   make start  
   ```  

7. **Installer les dépendances avec Composer** :  
   ```bash  
   make composer-install  
   ```  

8. **Nettoyer les conteneurs et les volumes** :  
   ```bash  
   make clean  
   ```  

N’oubliez pas de consulter les logs si vous rencontrez des problèmes :  
   ```bash  
   make logs  
   ```  

# Modèle de base de données pour **Filmoteca**

## Table `films`
| Colonne      | Type         | Description                       |
|--------------|--------------|-----------------------------------|
| `id`         | INT, PK, AI  | Identifiant unique du film       |
| `title`      | VARCHAR(255) | Titre du film                    |
| `year`       | YEAR         | Année de sortie                  |
| `type`       | VARCHAR(100) | Genre du film                    |
| `synopsis`   | TEXT         | Résumé du film                   |
| `director`   | VARCHAR(255) | Nom du réalisateur               |
| `deleted_at` | TIMESTAMP    | Date d'archivage dans la collection  |
| `created_at` | TIMESTAMP    | Date d'ajout dans la collection  |
| `updated_at` | TIMESTAMP    | Date de modification dans la collection  |

- **Description** : Contient les informations de base sur chaque film, y compris le titre, l’année de sortie, le genre, un synopsis et le nom du réalisateur.

## Table `reviews`
| Colonne      | Type         | Description                           |
|--------------|--------------|---------------------------------------|
| `id`         | INT, PK, AI  | Identifiant unique de l'avis         |
| `film_id`    | INT, FK      | Identifiant du film (clé étrangère)  |
| `rating`     | TINYINT      | Note sur 5                           |
| `comment`    | TEXT         | Commentaire de l’utilisateur         |
| `created_at` | TIMESTAMP    | Date de l'avis                       |

- **Relation** : Chaque avis est lié à un film spécifique par la colonne `film_id`, qui fait référence à `films.id`. Un film peut donc avoir plusieurs avis.

---

## Exemples de requêtes SQL

- **Récupérer tous les films** :
  ```sql
  SELECT * FROM films;
  ```

## Notes  
- Les commandes Make simplifient les interactions avec Docker et Docker Compose ;
- Utilisez `make` depuis le répertoire racine du projet pour exécuter correctement les commandes ;  
- En cas de problème, vérifiez que Docker et Docker Compose sont bien installés et fonctionnels.

Bon développement ! 🚀
