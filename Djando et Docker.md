
Liste d'erreurs courantes et de solutions spécifiques pour une application Django contenue dans Docker, utilisant PostgreSQL non conteneurisé, ainsi que des erreurs liées aux certificats SSL/TLS et à Angular en frontend.

### Erreurs Courantes avec Docker et Django

1. **Erreur : Impossible de se connecter à PostgreSQL**
   ```plaintext
   django.db.utils.OperationalError: could not connect to server: Connection refused
   ```
   **Solutions :**
    - Vérifiez les paramètres de connexion dans `settings.py` :
      ```python
      DATABASES = {
          'default': {
              'ENGINE': 'django.db.backends.postgresql',
              'NAME': 'mydatabase',
              'USER': 'myuser',
              'PASSWORD': 'mypassword',
              'HOST': 'db_host',  # Utilisez l'IP ou le nom d'hôte de votre serveur PostgreSQL
              'PORT': '5432',
          }
      }
      ```
    - Assurez-vous que PostgreSQL est en cours d'exécution sur le serveur spécifié :
      ```bash
      sudo service postgresql status
      ```

2. **Erreur : Django non synchronisé avec la base de données**
   ```plaintext
   django.db.utils.ProgrammingError: relation "mytable" does not exist
   ```
   **Solution :**
    - Exécutez les migrations pour synchroniser Django avec la base de données :
      ```bash
      docker-compose exec web python manage.py migrate
      ```

3. **Erreur : Fichiers statiques non servis correctement**
   ```plaintext
   404 Not Found: /static/
   ```
   **Solutions :**
    - Collectez les fichiers statiques :
      ```bash
      docker-compose exec web python manage.py collectstatic --noinput
      ```
    - Vérifiez la configuration du serveur web (par exemple, Nginx) pour s'assurer qu'il sert les fichiers statiques depuis le bon répertoire.

### Erreurs Courantes avec PostgreSQL

1. **Erreur : Accès refusé pour l'utilisateur**
   ```plaintext
   FATAL: password authentication failed for user "myuser"
   ```
   **Solution :**
    - Vérifiez les credentials et mettez à jour si nécessaire :
      ```sql
      ALTER USER myuser WITH PASSWORD 'mypassword';
      ```

2. **Erreur : Base de données inexistante**
   ```plaintext
   FATAL: database "mydatabase" does not exist
   ```
   **Solution :**
    - Créez la base de données :
      ```bash
      createdb mydatabase -U myuser
      ```

3. **Erreur : Connexion refusée par les règles de sécurité**
   ```plaintext
   FATAL: no pg_hba.conf entry for host "xxx.xxx.xxx.xxx", user "myuser", database "mydatabase", SSL off
   ```
   **Solution :**
    - Ajoutez une entrée dans `pg_hba.conf` pour permettre l'accès :
      ```plaintext
      host    all             all             0.0.0.0/0            md5
      ```
    - Redémarrez PostgreSQL pour appliquer les modifications :
      ```bash
      sudo service postgresql restart
      ```

### Erreurs Courantes avec Certificats SSL/TLS

1. **Erreur : Certificat auto-signé**
   ```plaintext
   NET::ERR_CERT_AUTHORITY_INVALID
   ```
   **Solution :**
    - Pour les environnements de développement, ajoutez une exception dans le navigateur.
    - Pour la production, obtenez un certificat signé par une autorité de certification comme Let's Encrypt.

2. **Erreur : Nom de domaine non correspondant**
   ```plaintext
   NET::ERR_CERT_COMMON_NAME_INVALID
   ```
   **Solution :**
    - Assurez-vous que le nom commun (CN) du certificat correspond au domaine utilisé.

3. **Erreur : Expiration du certificat**
   ```plaintext
   NET::ERR_CERT_DATE_INVALID
   ```
   **Solution :**
    - Renouvelez le certificat avant son expiration :
      ```bash
      sudo certbot renew
      ```

4. **Erreur : Problèmes CORS**
   ```plaintext
   Access to XMLHttpRequest at 'https://yourbackend.com/api' from origin 'https://yourfrontend.com' has been blocked by CORS policy
   ```
   **Solution :**
    - Configurez Django pour permettre les requêtes CORS :
      ```python
      # settings.py
      INSTALLED_APPS += ['corsheaders']
      MIDDLEWARE += ['corsheaders.middleware.CorsMiddleware']
 
      CORS_ALLOWED_ORIGINS = [
          "https://yourfrontend.com",
      ]
      ```

### Erreurs Courantes avec Angular en Frontend

1. **Erreur : Module non trouvé**
   ```plaintext
   ERROR in src/app/app.module.ts:3:29 - error TS2307: Cannot find module 'my-module'
   ```
   **Solution :**
    - Assurez-vous que le module est correctement installé :
      ```bash
      npm install my-module
      ```

2. **Erreur : Problèmes avec les dépendances**
   ```plaintext
   npm ERR! code ERESOLVE
   ```
   **Solution :**
    - Résolvez les conflits de dépendances en ajustant les versions dans `package.json` :
      ```json
      "dependencies": {
          "some-package": "^1.0.0",
          "conflicting-package": "^2.0.0"
      }
      ```

### Exemple de Configuration GitLab CI/CD pour Angular et Django

#### GitLab CI/CD pour Angular

```yaml
services:
  - docker:dind

cache:
  key: ${CI_COMMIT_REF_SLUG}
  paths:
    - node_modules/

stages:
  - build
  - deploy

build_stage_dev:
  image: node:12-alpine
  stage: build
  tags:
    - gpao_front
  environment:
    name: development
  only:
    - dev
  script:
    - npm install
    - ng build --configuration dev
  artifacts:
    paths:
      - dist

deploy_stage_dev:
  stage: deploy
  tags:
    - gpao_front
  environment:
    name: development
  only:
    - dev
  script:
    - apk add --no-cache openssh-client
    - mkdir -p ~/.ssh
    - echo "$DEV_SSH_PRIVATE_KEY" | base64 -d > ~/.ssh/id_rsa
    - chmod 600 ~/.ssh/id_rsa
    - echo -e "Host *\n\tStrictHostKeyChecking no\n\n" > ~/.ssh/config
    - scp -r dist/* gpao@10.13.112.173:/var/www/html/gpao-front
```

#### GitLab CI/CD pour Django

```yaml
stages:
  - deploy

deploy_stage_dev:
  stage: deploy
  tags:
    - gpao_main_api
  script:
    - apt-get update && apt-get install -y openssh-client
    - mkdir -p ~/.ssh
    - echo "$SSH_PRIVATE_KEY" | base64 --decode > ~/.ssh/id_rsa
    - chmod 600 ~/.ssh/id_rsa
    - echo -e "Host *\n\tStrictHostKeyChecking no\n\n" > ~/.ssh/config
    - ssh gpao@10.13.112.173 "cd /home/gpao/apps/gpao_main_api && git checkout dev && git pull origin dev && docker compose -f docker-compose-dev.yml up -d --build"
  only:
    - dev
```

En suivant ces configurations et solutions, vous pouvez résoudre les problèmes courants rencontrés lors du déploiement et de l'exécution d'une application utilisant Angular en frontend, Django en backend, Docker pour la conteneurisation, et PostgreSQL comme base de données, avec une attention particulière aux erreurs liées aux certificats SSL/TLS.
