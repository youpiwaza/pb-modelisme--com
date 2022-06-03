# 💻 How to work locally

All you need to know about how to setup a local developpment environment for this project.

## 💻👷 Local environment

For me, it's on Windows > [WSL / Windows Subsystem for Linux](https://docs.microsoft.com/fr-fr/windows/wsl/install) & [docker desktop](https://www.docker.com/products/docker-desktop/).

Cf. my [setup project](https://github.com/youpiwaza/install-dev-env) if you want to get the same setup.

## 🐳 Docker compose setup

Using a dedicated `docker compose file`, with sensitive informations stored on another repo, using the `_secret` prefix.

Dockerhub images :

- [Dockerhub WordPress](https://hub.docker.com/_/wordpress)
- [Dockerhub mysql](https://hub.docker.com/_/mysql)
- [Dockerhub phpmyadmin](https://hub.docker.com/_/phpmyadmin)

Local/Dev file synchronisation through binded volume :

- Local : (this) Cloned repo > `wordpress/` folder data injection in the WordPress container
- Host : Cloned repo > Apache serving the `wordpress/` folder

### Usual commands

```bash
# Access local folder
cd /mnt/c/Users/masam/Documents/_dev/_current/pb-modelisme--com/local_environnement

# Start composition
## Interactive, with logs
docker-compose -f pb.yml up

# Detached
docker-compose -f pb.yml up -d

# Stop
docker-compose -f pb.yml stop

# Remove
docker-compose -f pb.yml rm

# 🧽 1 liner remove previous installations
docker-compose -f pb.yml stop && docker-compose -f pb.yml rm && docker volume rm local_environnement_db

# 🧽 Total clean docker environement
docker system prune -af && \
docker image prune -af && \
docker volume rm local_environnement_db

# 🐛 Bug docker credentials can't pull from docker compose fix : pull images from docker CLI first
docker pull wordpress:6.0.0-php8.1-apache && \
docker pull mysql:latest && \
docker pull phpmyadmin:latest

# 📄🐛 Logs (via another terminal if started as interactive)
## Gather containers names
docker ps
## Display logs, tail -f style
docker logs -f local_environnement_wordpress_1
docker logs -f local_environnement_db_1
docker logs -f local_environnement_phpmyadmin_1

## 📈 Resources consumtions
docker stats
```

Local uris:

- [Wordpress > localhost:8080](http://localhost:8080)
- [PhpMyAdmin > localhost:8081](http://localhost:8081)

Note: You'll need to re-install WordPress the first time (or after `db` volume removal),
or inject the database ; 🚨 all WordPress URIs are flat, with domain name.

### WP-CLI usage

See the [craft documentation for details](../_docs/craft-and-tests/02-local-docker-wp-cli/).

```bash
# Get container name
docker ps

# Log into the container
docker exec                             \
    -it                                 \
    --user xfs                          \
    local_environnement_wordpress-cli_1 \
    bash

# 🐛👷 In case of bad user/permissions, go in the container & adjust user (if run as non root or something)
> whoami
# xfs
> ls -lah
# [...]
# -rwxr--r--    1 1000     1000         561 Jun  1 10:28 .htaccess
# 💥               ^        ^

docker exec                             \
    -it                                 \
    --user 1000:1000                    \
    local_environnement_wordpress-cli_1 \
    bash
> whoami
# ✅ whoami: unknown uid 1000

# Execute WP-CLI commands
##      https://developer.wordpress.org/cli/commands/

# Safe test command, no edits : display users
> wp user list

## Update wp
> wp core update

## Update translations ~💩 Says ok but isn't updated lol
> wp language core update

## Install a plugin
> wp plugin install hello-dolly

## Activate plugin
> wp plugin activate hello-dolly

## Deactivate plugin
> wp plugin deactivate hello-dolly

## Delete plugin
> wp plugin delete hello-dolly
```
