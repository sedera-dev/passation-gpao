
## Migration de l'application ERP d'un serveur à un autre.
Ce plan inclut également des instructions pour migrer la base de données PostgreSQL qui n'est pas sous Docker.

---

## Étape 0: Préparation de la machine physique
- **Installation du système d'exploitation**:
    - Assurez-vous que la machine physique a un système d'exploitation approprié et à jour.
    - Installez toutes les mises à jour de sécurité.

- **Installation de Docker et Docker Compose**:
    - Installez Docker et Docker Compose, puis assurez-vous que le service Docker démarre automatiquement.
    - Configurez les autorisations Docker pour l'utilisateur qui doit gérer les conteneurs sans privilèges `sudo`.

- **Configuration du réseau et des ports**:
    - **Pare-feu**:
        - Configurez les règles de pare-feu pour permettre uniquement le trafic nécessaire. Par exemple, ouvrez les ports pour HTTP (80), HTTPS (443), et d'autres services que votre application pourrait utiliser (comme PostgreSQL, si nécessaire).
    - **Vérification des ports**:
        - Utilisez des outils tels que `netstat` ou `ss` pour lister les ports ouverts sur le serveur:
          ```bash
          sudo netstat -tuln
          ```
        - Vérifiez que les ports nécessaires pour votre application sont ouverts et non utilisés par d'autres services.
    - **Configuration des règles de sécurité**:
        - Assurez-vous que les ports ouverts sont sécurisés. Par exemple, si vous ouvrez le port 5432 pour PostgreSQL, assurez-vous qu'il n'est accessible que par des clients autorisés (par exemple, en utilisant des règles de pare-feu).
    - **Configuration des ports Docker**:
        - Si vous utilisez des conteneurs Docker qui exposent des ports, assurez-vous que les ports correspondants sont configurés correctement dans `docker-compose.yml` ou d'autres fichiers de configuration.
    - **Routage des ports**:
        - Vérifiez les configurations de routage de port si vous utilisez un proxy inverse (comme Nginx) ou d'autres mécanismes de routage.

- **Surveillance et gestion du système**:
    - Installez des outils de surveillance pour vérifier l'utilisation des ressources du système et des conteneurs.
    - Configurez des alertes pour être informé des activités suspectes ou des ports qui devraient être fermés.

---

## Étape 1: Préparation et planification
- **Audit des composants existants**:
    - Identifiez les composants qui doivent être migrés, y compris le code de l'application, les fichiers statiques, les fichiers médias, et les bases de données.
    - Notez les versions des logiciels et des dépendances utilisés (Django, Docker, PostgreSQL, etc.).

- **Préparation de l'environnement cible**:
    - Assurez-vous que le nouveau serveur a suffisamment de ressources pour exécuter l'application.
    - Configurez le réseau et les règles de pare-feu pour permettre la communication avec les services nécessaires.

## Étape 2: Sauvegarde et transfert de la base de données
- **Sauvegarde de PostgreSQL**:
    - Arrêtez temporairement les opérations qui modifient la base de données pour éviter des incohérences.
    - Utilisez `pg_dump` pour créer une sauvegarde complète de la base de données.
    - Stockez la sauvegarde dans un endroit sûr.

- **Transfert de la sauvegarde**:
    - Transférez le fichier de sauvegarde vers le nouveau serveur, en utilisant un protocole sécurisé comme `scp` ou `rsync`.
    - Assurez-vous que le fichier de sauvegarde n'est pas corrompu après le transfert.

## Étape 3: Migration des composants de l'application
- **Transfert du code de l'application**:
    - Copiez le code source de l'application Django depuis l'ancien serveur vers le nouveau serveur.
    - Si vous utilisez un outil de gestion de versions comme Git, assurez-vous d'avoir le bon état du code.

- **Transfert des fichiers statiques et médias**:
    - Copiez les fichiers statiques et médias vers le nouveau serveur.
    - Assurez-vous que les permissions et la structure des répertoires sont correctes.

- **Configuration de Docker sur le nouveau serveur**:
    - Installez Docker et Docker Compose si nécessaire.
    - Copiez les fichiers Docker et Docker Compose sur le nouveau serveur.
    - Configurez les variables d'environnement nécessaires.

## Étape 4: Configuration de PostgreSQL sur le nouveau serveur
- **Installation de PostgreSQL**:
    - Installez la même version de PostgreSQL sur le nouveau serveur.
    - Configurez les utilisateurs, rôles, et permissions nécessaires.

- **Restauration de la base de données**:
    - Utilisez `pg_restore` pour restaurer la base de données à partir de la sauvegarde.
    - Vérifiez que la base de données a été restaurée correctement.

## Étape 5: Mise en service de l'application sur le nouveau serveur
- **Démarrage de l'application Django**:
    - Utilisez Docker Compose pour démarrer les conteneurs.
    - Assurez-vous que tous les services démarrent sans erreurs.

- **Configuration des fichiers statiques et médias**:
    - Si nécessaire, exécutez `collectstatic` pour rassembler les fichiers statiques.
    - Vérifiez que les fichiers médias sont accessibles.

- **Tests fonctionnels**:
    - Effectuez des tests pour vous assurer que l'application fonctionne comme prévu.
    - Corrigez les erreurs ou les problèmes rencontrés.

## Étape 6: Validation et nettoyage
- **Validation de la migration**:
    - Assurez-vous que toutes les fonctionnalités de l'application sont opérationnelles.
    - Effectuez des tests de charge si nécessaire pour garantir les performances.

- **Nettoyage de l'ancien serveur**:
    - Une fois que vous avez confirmé que tout fonctionne sur le nouveau serveur, nettoyez l'ancien serveur.
    - Supprimez les fichiers de sauvegarde inutiles et les configurations liées à l'application.

- **Documentation et plan de secours**:
    - Documentez les étapes de la migration pour référence future.
    - Préparez un plan de secours au cas où vous auriez besoin de revenir en arrière.

En suivant ces étapes, vous devriez être en mesure de migrer votre application ERP ainsi que votre base de données PostgreSQL avec succès d'un serveur à un autre.
