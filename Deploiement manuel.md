## Déploiement manuel

Pour réaliser un déploiement manuel en utilisant le script GitLab CI/CD fourni, vous devez déclencher les tâches manuellement depuis l'interface utilisateur de GitLab. Voici les étapes détaillées pour effectuer un déploiement manuel sur les environnements de pré-production et de production.

### Prérequis
- Vous devez avoir les permissions nécessaires pour déclencher les jobs manuels sur le projet GitLab.
- Assurez-vous que le pipeline a été exécuté avec succès jusqu'à l'étape de déploiement souhaitée (pré-production ou production).

### Étapes pour un Déploiement Manuel

#### Déploiement sur l'Environnement de Pré-Production

1. **Accédez au Projet GitLab** :
    - Connectez-vous à votre instance GitLab.
    - Naviguez jusqu'au projet concerné.

2. **Accédez à la Section Pipelines** :
    - Cliquez sur l'onglet "CI/CD" dans le menu latéral.
    - Sélectionnez "Pipelines" pour voir la liste des pipelines exécutés.

3. **Choisissez le Pipeline** :
    - Repérez le pipeline le plus récent ou celui spécifique que vous souhaitez utiliser pour le déploiement.
    - Cliquez sur l'identifiant du pipeline pour voir les détails de son exécution.

4. **Déclenchez le Déploiement Manuel** :
    - Dans le pipeline détaillé, cherchez l'étape "deploy_to_preprod".
    - Vous devriez voir un bouton "Play" ou "Manual" à côté de cette étape. Cliquez dessus pour démarrer le job de déploiement vers la pré-production.

5. **Surveillance du Déploiement** :
    - Une fois le job lancé, surveillez les logs d'exécution pour vous assurer que le déploiement se passe correctement.
    - Si tout se passe bien, le job devrait terminer avec succès et votre application sera déployée sur le serveur de pré-production.

#### Déploiement sur l'Environnement de Production

1. **Répétez les Étapes 1 à 3** de la section précédente pour accéder au pipeline souhaité.

2. **Déclenchez le Déploiement Manuel** :
    - Dans le pipeline détaillé, cherchez l'étape "deploy_to_prod".
    - Cliquez sur le bouton "Play" ou "Manual" à côté de cette étape pour démarrer le job de déploiement vers la production.

3. **Surveillance du Déploiement** :
    - Comme pour le pré-production, surveillez les logs d'exécution pour vous assurer que le déploiement se passe correctement.
    - Si le job termine avec succès, votre application sera déployée sur le serveur de production.

### Vérification Post-Déploiement

- **Pré-Production** : Visitez l'URL de pré-production (https://staging-erp.futurmap.local) pour vérifier que l'application fonctionne correctement.
- **Production** : Visitez l'URL de production (https://erp.futurmap.local) pour vérifier que l'application est bien déployée et fonctionne comme attendu.

### Points à Surveiller

- Assurez-vous que les variables d'environnement (comme les identifiants Docker) sont correctement configurées dans les paramètres GitLab CI/CD du projet.
- Vérifiez que les scripts de déploiement (par exemple, `increment_version_and_tag_image.sh`) sont présents et ont les permissions nécessaires.

En suivant ces étapes, vous pouvez effectuer un déploiement manuel sur vos environnements de pré-production et de production en utilisant GitLab CI/CD.
