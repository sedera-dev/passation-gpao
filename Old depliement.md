## Instructions de Déploiement Manuel

4.  **Validez le Déploiement**

### Détails de la commande

1.  **ssh gpao@10.13.112.152** :

    -   **ssh** : Ouvre une session SSH.
    -   **gpao@10.13.112.152** : Connexion à l'utilisateur `gpao` sur le serveur à l'adresse IP `10.13.112.152`.
2.  **"cd /home/gpao/erp/gpao_main_api && ... "** :

    -   **cd /home/gpao/erp/gpao_main_api** : Change le répertoire courant vers `/home/gpao/erp/gpao_main_api`.
3.  **git checkout preprod && ...** :

    -   **git checkout preprod** : Change la branche active de Git vers `preprod`.
4.  **git pull origin preprod && ...** :

    -   **git pull origin preprod** : Met à jour la branche `preprod` en récupérant les dernières modifications depuis le dépôt distant `origin`.
5.  **docker-compose -f docker-compose-preprod.yml up -d --build** :

    -   **docker-compose -f docker-compose-preprod.yml** : Utilise le fichier `docker-compose-preprod.yml` pour définir les services Docker.
    -   **up -d --build** : Lance les conteneurs en mode détaché (`-d`) et les reconstruit (`--build`).

    -   Après le déploiement, vérifiez les logs des applications et assurez-vous que les services sont en cours d’exécution comme prévu.

### Warning !!!
Pendant le deploiement, il faut toujours s'assurer et bien verifier les certificats des applications

