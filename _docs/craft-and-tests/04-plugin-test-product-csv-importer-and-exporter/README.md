# Plugin test : Product CSV Importer and Exporter

Testing the [WC built-in importer](https://woocommerce.com/document/product-csv-importer-exporter/).

Docs:

- Fichier d'exemple : [sample_products.csv](https://github.com/woocommerce/woocommerce/blob/master/sample-data/sample_products.csv)
- [Schéma de colonnes à respecter](https://github.com/woocommerce/woocommerce/wiki/Product-CSV-Import-Schema#csv-columns-and-formatting).
- Syntaxe & normes : [guidelines](https://woocommerce.com/document/product-csv-importer-exporter/#general-guidelines)

## Export

RAS, tout fonctionne correctement, y compris avec les champs personnalisés (🚨 Penser à cocher la case)

## Import

🚨 Pas mal de termes sont traduits au niveau des noms de colonnes, ex "SKU" est traduit en "UGS".

RAS sinon

## Mise à jour

Création d'un csv dupliqué, ou un seul produit est modifié.

KO > Produit non valide. Pas d'autre détail. Lié à l'ID dans woocommerce ?

OK > Test en faisant un export clean depuis WC, des nouveaux produits en CSV, puis modification à la main puis renvoi.

OK > Test en virant la colonne ID (SKU uniquement).

🚨 Notes:

- Si `id` ET `sku` sont fournis, les deux doivent correspondrent, sinon produit non valide
- Si on souhaite mettre à jour un produit uniquement à partir du `sku`, virer la colonne (& les valeurs) `id`
