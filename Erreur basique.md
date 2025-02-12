
Liste des erreurs courantes que vous pourriez rencontrer lors du déploiement avec GitLab CI/CD, ainsi que des solutions pour chaque problème :

### 1. Erreur de Connexion à Docker Hub
**Erreur :**
```plaintext
Error: Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?
```
**Solution :**
- Assurez-vous que le démon Docker est en cours d'exécution sur le runner.
- Vérifiez les permissions de Docker pour l'utilisateur courant.

### 2. Authentification Docker Hub Échouée
**Erreur :**
```plaintext
Error response from daemon: Get https://registry-1.docker.io/v2/: unauthorized: incorrect username or password
```
**Solution :**
- Vérifiez que les variables `DOCKER_USERNAME` et `DOCKER_PASSWORD` sont correctement configurées dans les variables CI/CD de GitLab.
- Assurez-vous que le mot de passe n'expire pas ou n'est pas réinitialisé.

### 3. Échec de la Construction de l'Image Docker
**Erreur :**
```plaintext
Step 1/10 : FROM python:3.8-slim
Get https://registry-1.docker.io/v2/: net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers)
```
**Solution :**
- Vérifiez la connectivité réseau du runner.
- Assurez-vous que le fichier Dockerfile est présent et correctement configuré.

### 4. Problèmes de Permissions
**Erreur :**
```plaintext
/bin/bash: ./increment_version_and_tag_image.sh: Permission denied
```
**Solution :**
- Assurez-vous que le script a les permissions d'exécution (`chmod +x increment_version_and_tag_image.sh`).
- Vérifiez que la commande `before_script` pour modifier les permissions est correctement exécutée.

### 5. Erreur SSH Lors du Déploiement
**Erreur :**
```plaintext
ssh: connect to host 10.13.112.173 port 22: Connection refused
```
**Solution :**
- Vérifiez que le serveur de destination est en cours d'exécution et accessible depuis le runner.
- Assurez-vous que les clés SSH sont correctement configurées et que l'utilisateur a les permissions nécessaires.

### 6. Échec du Pull de l'Image Docker
**Erreur :**
```plaintext
Error response from daemon: manifest for gpao/gpao_main_api:v1.0.0 not found: manifest unknown: manifest unknown
``>
**Solution :**
- Assurez-vous que l'image a été correctement construite et poussée vers le registre Docker.
- Vérifiez que la version spécifiée dans `VERSION_FILE` correspond à une image existante.

### 7. Problèmes de Variables d'Environnement
**Erreur :**
```plaintext
/bin/bash: line 0: cd: $DOCKER_REGISTRY/$IMAGE_NAME: No such file or directory
```
**Solution :**
- Vérifiez que toutes les variables d'environnement sont correctement définies dans les paramètres CI/CD de GitLab.
- Assurez-vous qu'il n'y a pas de fautes de frappe dans les noms des variables.

### 8. Problèmes de Syntaxe YAML
**Erreur :**
```plaintext
jobs:deploy_to_dev config contains unknown keys: script:
``>
**Solution :**
- Validez votre fichier `.gitlab-ci.yml` avec un validateur YAML pour vous assurer qu'il est correctement formé.
- Vérifiez que toutes les sections et indentations sont correctes.

### 9. Échec du Redémarrage du Conteneur Docker
**Erreur :**
```plaintext
docker: Error response from daemon: Conflict. The container name "/gpao_main_api" is already in use by container "123abc...".
``>
**Solution :**
- Assurez-vous que les commandes `docker stop` et `docker rm` sont correctement exécutées pour arrêter et supprimer l'ancien conteneur.
- Utilisez l'option `--force` pour forcer l'arrêt et la suppression du conteneur si nécessaire.

### 10. Erreurs de Script Personnalisé
**Erreur :**
```plaintext
./increment_version_and_tag_image.sh: line 10: syntax error: unexpected end of file
``>
**Solution :**
- Revoyez le script `increment_version_and_tag_image.sh` pour vous assurer qu'il ne contient pas d'erreurs de syntaxe.
- Assurez-vous que le script est complet et ne manque pas de parties essentielles.

### Résolution des Problèmes Généraux
1. **Logs et Debugging** :
   - Consultez les logs détaillés de chaque job pour obtenir plus d'informations sur les erreurs.
   - Utilisez des commandes `echo` dans vos scripts pour ajouter des messages de debug.

2. **Documentation et Support** :
   - Consultez la documentation officielle de GitLab CI/CD pour des configurations spécifiques et des exemples.
   - Recherchez des solutions sur les forums communautaires ou demandez de l'aide si vous êtes bloqué.

En suivant ces solutions pour les problèmes courants, vous devriez pouvoir résoudre la plupart des erreurs que vous rencontrez lors du déploiement avec GitLab CI/CD.
