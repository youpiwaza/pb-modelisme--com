# Création du fichier docker compose

Basé sur l'image officielle [WordPress](https://hub.docker.com/_/wordpress).

## Docs

🚨 L'image `5.9.3-fpm` est basée sur PHP 7.4, partir sur ~~`5.9.3-php8.0-fpm`~~ WordPress 6.0 `beta-6.0-RC4-php8.1-fpm`.

Note: Tourne sous apache.

```yml
# Docker example
version: '3.1'

services:

  wordpress:
    image: wordpress
    restart: always
    ports:
      - 8080:80
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: exampleuser
      WORDPRESS_DB_PASSWORD: examplepass
      WORDPRESS_DB_NAME: exampledb
    volumes:
      - wordpress:/var/www/html

  db:
    image: mysql:5.7
    restart: always
    environment:
      MYSQL_DATABASE: exampledb
      MYSQL_USER: exampleuser
      MYSQL_PASSWORD: examplepass
      MYSQL_RANDOM_ROOT_PASSWORD: '1'
    volumes:
      - db:/var/lib/mysql

volumes:
  wordpress:
  db:
```

### ✅📌 Test vanilla

Doc [docker compose up](https://docs.docker.com/compose/reference/up/)

```bash
cd /mnt/c/Users/masam/Documents/_dev/_current/pb-modelisme--com/_docs/craft-and-tests/01-craft-docker-compose-file

# -f > specify file
docker-compose -f example.yml up

# 🧽 Clean docker environement
# 🚨 Mauvais setup de la base de données si les volumes déjà en place > docker volume ls
docker system prune -af
docker image prune -af
docker volume rm 01-craft-docker-compose-file_db
docker volume rm 01-craft-docker-compose-file_wordpress
```

Local uri : [localhost:8080](http://localhost:8080)

---

## Notre docker compose

- ✅ Prendre les dernières images a jour si possible
  - 💩 phpfpm > `./KO php-fpm`
    - 🚨💩 WordPress php fpm n'a pas de serveur WTF xD besoin de coller un nginx au cul
    - Récupération d'un de mes anciens projet de test [nginx + sql + php-fpm + adminer](https://github.com/youpiwaza/server-related-tutorials/tree/master/01-docker/04-my-tests/03-compose-nginx-php-sql)
      - Adaptation > quelques tests > pas de perte de temps
  - ✅ images `wordpress:php8.1` & `mysql:latest`
- ✅ Accès aux fichiers sources en local
- ✅ Mots de passes aléatoires gérés via [secrets](https://docs.docker.com/engine/swarm/secrets/#use-secrets-in-compose)
  - ✅ 🔒️ à passer dans un fichier `_secret` dans le repo dédié
- ✅ Virer les warnings restants à l'initialisation
  - ✅ No empty root password
  - ❌ `Insecure configuration for --pid-file` > Trop rieng sur le net ni dans la doc > skip
- Ajouter image phpmyadmin ou adminer
- Ajouter les [bonnes pratiques DC](https://github.com/youpiwaza/docker-compose-curated-example).

Docs

- [dockerhub mysql](https://hub.docker.com/_/mysql)
- [dockerhub adminer](https://hub.docker.com/_/adminer)

```bash
cd /mnt/c/Users/masam/Documents/_dev/_current/pb-modelisme--com/_docs/craft-and-tests/01-craft-docker-compose-file
docker-compose ls

# 🧽 1 liner remove previous installations
docker-compose -f pb.yml stop && docker-compose -f pb.yml rm && docker volume rm 01-craft-docker-compose-file_db && docker volume rm 01-craft-docker-compose-file_wordpress

# Interactive, with logs
docker-compose -f pb.yml up

# Detached
docker-compose -f pb.yml up -d

# Stop & rm
docker-compose -f pb.yml stop && docker-compose -f pb.yml rm

# 🧽 Total clean docker environement
docker system prune -af && \
docker image prune -af && \
docker volume rm 01-craft-docker-compose-file_db && \
docker volume rm 01-craft-docker-compose-file_wordpress
```

Local uri : [localhost:8080](http://localhost:8080)
