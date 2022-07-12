# PB-Modelisme.com

Website rework, with basic WordPress setup, and precedent website's datas import/restructuration.

## Usual comands

```bash
stuff
```

## Installation

1. Sur un serveur web disposant d'une base de données, ainsi que des accès..
   1. Je recommande apache ou Nginx, avec PHP 8+
   2. HTTPS est indispensable pour les paiements en ligne.
2. Installer WordPress (version 6.0 lors du dev)
3. Installer le thème elegantthemes > Divi & l'activer
4. Installer les plugins suivants
   1. advanced-custom-fields-pro
   2. all-in-one-wp-migration
   3. woocommerce
5. Récupérer et injecter `wp-content/uploads/`
6. Récupérer la base de donnée fournie (brute SQL ou via all in one wp migration) et l'injecter
7. Admin WordPress > Réglages > Permaliens > Enregistrer (afin de régénérer le .htaccess)

### 🔒️ Sensible informations

Sensible files won't be versionned in this public project.

You can use the `_secret` suffix in your 'secret' file name.

Instead, they will be stored on a *private* repository, containing the same tree folder.

Just copy/paste the 'secrets' repository on top of this one **on your local machine only**.

And don't forget to update it if some creditentials changes.

## Documentation

This project's documentation can be found in the [_docs](./_docs/) folder.

## Technologies

- html

## Ressources

- [html](https://developer.mozilla.org/fr/docs/Web/HTML)

## Credits

Masamune / Maxime Chevasson
