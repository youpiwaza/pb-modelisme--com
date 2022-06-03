# Local > implement wp cli

Through the official [dockerhub image > "wordpress:cli"](https://hub.docker.com/_/wordpress).

## 💩 From docker run

```bash
# 💩
docker run -it --rm \
    --volumes-from local_environnement_wordpress_1 \
    --network container:local_environnement_wordpress_1 \
    -e WORDPRESS_DB_HOST=db \
    -e WORDPRESS_DB_NAME=XXX \
    -e WORDPRESS_DB_PASSWORD=XXX \
    -e WORDPRESS_DB_USER=XXX \
    -e WORDPRESS_TABLE_PREFIX_FILE=XXX \
    wordpress:cli-2.6.0-php8.1 user list
# /usr/local/bin/docker-entrypoint.sh: exec: line 11: user: not found

# 💩, Tried with 33:33, 82:82, xfs
# cf. https://hub.docker.com/_/wordpress > Running as an arbitrary user
docker run \
    -e HOME=/tmp \
    -e WORDPRESS_DB_HOST=db \
    -e WORDPRESS_DB_NAME=XXX \
    -e WORDPRESS_DB_PASSWORD=XXX \
    -e WORDPRESS_DB_USER=XXX \
    -e WORDPRESS_TABLE_PREFIX_FILE=XXX \
    -it \
    --network container:local_environnement_wordpress_1 \
    --rm \
    --user xfs \
    --volumes-from local_environnement_wordpress_1 \
    wordpress:cli-2.6.0-php8.1 user list
# Error from entrypoint > https://github.com/docker-library/wordpress/blob/376f01affcaacac50d8416b5f7f4abb34d6b60d2/cli/php8.1/alpine/docker-entrypoint.sh
```

## ✅ Trying to add to docker compose

Following this [SO answer](https://stackoverflow.com/a/51001043).

```yml
# Original example w. annotations
services:
    # [...]
    wordpress-cli:
        # vstm: The sleep 10 is required so that the command is run after
        # mysql is initialized. Depending on your machine this might take
        # longer or it can go faster.
        # max: 10 isn't enough with first installation, and is weird.
        # command: >
        #     /bin/sh -c '
        #     sleep 20;
        #     wp core install --path="/var/www/html" --url="http://localhost:8000" --title="Local Wordpress By Docker" --admin_user=admin --admin_password=secret --admin_email=foo@bar.com
        #     '

        # Better trap sleep, waiting for docker exec -it
        #   https://stackoverflow.com/questions/31870222/how-can-i-keep-a-container-running-on-kubernetes
        #       wp-cli is running on alpine
        command: >
            /bin/sh -c "trap : TERM INT; sleep 9999999999d & wait"
        
        depends_on:
            - db
            - wordpress

        image: wordpress:cli

        # vstm: This is required to run wordpress-cli with the same
        # user-id as wordpress. This way there are no permission problems
        # when running the cli
        user: xfs
        
        # vstm: add shared volume
        volumes:
        - wp_data:/var/www/html

        # 🚨 Ajout environnement(secrets) & network & proper volumes
```

Docker compose commands

```bash
cd /mnt/c/Users/masam/Documents/_dev/_current/pb-modelisme--com/_docs/craft-and-tests/02-local-docker-wp-cli

docker-compose -f pb.yml up
docker-compose -f pb.yml stop 
docker-compose -f pb.yml rm

# ✅ Ok at startup, even if wordpress isn't installed

# Now we need to connect into the container, in order to send instructions
docker ps
# 02-local-docker-wp-cli_wordpress-cli_1

# ✅ Ok
# No bin/ash despite alpine ?
docker exec \
    -it \
    --user xfs \
    02-local-docker-wp-cli_wordpress-cli_1 \
    bash

> wp user list
# +----+------------+--------------+-------------+---------------------+---------------+
# | ID | user_login | display_name | user_email  | user_registered     | roles         |
# +----+------------+--------------+-------------+---------------------+---------------+
# | 1  | mlk        | mlk          | mlk@mlk.com | 2022-06-03 11:45:16 | administrator |
# +----+------------+--------------+-------------+---------------------+---------------+
> exit

# ✅ Execute a command without interactivity
docker exec \
    --user xfs \
    02-local-docker-wp-cli_wordpress-cli_1 \
    bash -c "wp user list"

# ID      user_login      display_name    user_email      user_registered roles
# 1       mlk     mlk     mlk@mlk.com     2022-06-03 11:45:16     administrator
```

Note: 💚 Adding a healthcheck on `/etc/alpine-release`

## 📌 Usual wp-cli commands

Test the access to files & database update

```bash
# ✅ Execute a command with interactivity
docker exec \
    -it \
    --user xfs \
    02-local-docker-wp-cli_wordpress-cli_1 \
    bash

## ✅ Update wp
> wp core update
# Success: WordPress is up to date.

# 💩 Update translations ~Says ok but isn't updated lol
> wp language core update
# Success: Translations are up to date.

#       WP-CLI > plugins commands > https://developer.wordpress.org/cli/commands/plugin/
## ✅ Install a plugin
> wp plugin install woocommerce
# Installing WooCommerce (6.5.1)
# 🚨 Warning: Failed to create directory '/etc/X11/fs/.wp-cli/cache/': mkdir(): Permission denied.

#       DH > Running as an arbitrary user
#       (possibly also something like -e HOME=/tmp depending on the wp command invoked)
#           📌 .yml > environment: > HOME: /tmp ?

# Téléchargement du paquet d’installation depuis https://downloads.wordpress.org/plugin/woocommerce.6.5.1.zip…
# Décompression de l’archive de l’extension...
# Installation de l’extension...
# L’extension a bien été installée.
# Success: Installed 1 of 1 plugins.

## ✅ Install a plugin
> wp plugin install woocommerce

## ✅ Activate plugin
> wp plugin activate woocommerce
# Success: Activated 1 of 1 plugins.

## ✅ Deactivate plugin
> wp plugin deactivate woocommerce

## ✅ Delete plugin
> wp plugin delete woocommerce
```
