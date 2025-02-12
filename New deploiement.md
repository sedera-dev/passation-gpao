
## Nouvelle deploiement

Ce script est un fichier de configuration pour un pipeline CI/CD (Intégration Continue/Déploiement Continu) utilisant GitLab CI/CD. Il définit les différentes étapes nécessaires pour construire, tester et déployer une application Docker. Voici une explication détaillée de chaque section du script :

### Stages

```yaml
stages:
  - build
  - deploy_dev
  - deploy_preprod
  - deploy_prod
```

Ces lignes définissent les différentes phases du pipeline :
- `build`: Construire l'image Docker.
- `deploy_dev`: Déployer l'image sur l'environnement de développement.
- `deploy_preprod`: Déployer l'image sur l'environnement de pré-production.
- `deploy_prod`: Déployer l'image sur l'environnement de production.

### Variables

```yaml
variables:
  DOCKER_REGISTRY: "gpao"
  IMAGE_NAME: "gpao_main_api"
  VERSION_FILE: "/home/gpao/ERP_STATIC_FILE/VERSION"
  DOCKER_USERNAME: "francogpao"
  DOCKER_PASSWORD: "AicaeL0e_"
```

Ces variables globales sont utilisées à plusieurs endroits dans le script :
- `DOCKER_REGISTRY` et `IMAGE_NAME` spécifient où l'image Docker sera stockée et son nom.
- `VERSION_FILE` indique le fichier contenant la version de l'application.
- `DOCKER_USERNAME` et `DOCKER_PASSWORD` sont les informations de connexion pour Docker Hub.

### Before Script

```yaml
before_script:
  - chmod +x increment_version_and_tag_image.sh  # Grant execute permission to the script
```

Cette section configure les prérequis nécessaires avant l'exécution de chaque étape. Ici, elle donne les permissions d'exécution au script `increment_version_and_tag_image.sh`.

### Build Image

```yaml
build_image:
  stage: build
  tags:
    - gpao_main_api
  script:
    - echo "Building image..."
    - docker build -t $DOCKER_REGISTRY/$IMAGE_NAME:latest .
    - echo "Incrementing version and tagging image..."
    - ./increment_version_and_tag_image.sh
  only:
    - dev
```

Cette étape construit l'image Docker :
- Elle appartient à la phase `build`.
- Le tag `gpao_main_api` spécifie les runners qui peuvent exécuter cette tâche.
- Le script construit l'image Docker et incrémente la version en utilisant un script personnalisé.
- Elle ne s'exécute que sur la branche `dev`.

### Deploy to Docker Hub

```yaml
deploy_to_docker_hub:
  stage: deploy_dev
  tags:
    - gpao_main_api
  script:
    - echo "Logging in to Docker Hub..."
    - echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
    - echo "Pushing image to Docker Hub..."
    - docker push $DOCKER_REGISTRY/$IMAGE_NAME:v$(cat $VERSION_FILE)
  only:
    - dev
```

Cette étape déploie l'image Docker sur Docker Hub :
- Elle appartient à la phase `deploy_dev`.
- Le script connecte Docker à Docker Hub et pousse l'image avec la nouvelle version.
- Elle ne s'exécute que sur la branche `dev`.

### Deploy to Development

```yaml
deploy_to_dev:
  stage: deploy_dev
  tags:
    - gpao_main_api
  script:
    - echo "Deploying to development server..."
    - ssh gpao@10.13.112.173 "docker pull $DOCKER_REGISTRY/$IMAGE_NAME:v$(cat $VERSION_FILE)
        && docker stop gpao_main_api || true && docker rm gpao_main_api || true
        && docker run -d --name gpao_main_api $DOCKER_REGISTRY/$IMAGE_NAME:v$(cat $VERSION_FILE)"
  only:
    - dev
  environment:
    name: dev
    url: https://dev-gpao.futurmap.local
```

Cette étape déploie l'image sur le serveur de développement :
- Elle appartient à la phase `deploy_dev`.
- Le script se connecte au serveur de développement, récupère l'image Docker et la déploie en arrêtant et supprimant l'ancienne instance si elle existe, puis en démarrant une nouvelle instance.
- Elle ne s'exécute que sur la branche `dev`.
- Elle définit l'environnement `dev` avec une URL spécifique.

### Deploy to Preprod

```yaml
deploy_to_preprod:
  stage: deploy_preprod
  tags:
    - gpao_main_api
  script:
    - echo "Deploying to preprod server..."
    - ssh gpao@10.13.112.152 "docker pull $DOCKER_REGISTRY/$IMAGE_NAME:v$(cat $VERSION_FILE)
        && docker stop gpao_main_api || true && docker rm gpao_main_api || true
        && docker run -d --name gpao_main_api $DOCKER_REGISTRY/$IMAGE_NAME:v$(cat $VERSION_FILE)"
  when: manual  # Manual trigger for deployment to preprod
  only:
    - preprod
  environment:
    name: preprod
    url: https://staging-erp.futurmap.local
```

Cette étape déploie l'image sur le serveur de pré-production :
- Elle appartient à la phase `deploy_preprod`.
- Le déploiement est similaire à celui de l'environnement de développement, mais sur le serveur de pré-production.
- Cette étape est déclenchée manuellement (`when: manual`).
- Elle ne s'exécute que sur la branche `preprod`.
- Elle définit l'environnement `preprod` avec une URL spécifique.

### Deploy to Prod

```yaml
deploy_to_prod:
  stage: deploy_prod
  tags:
    - gpao_main_api
  script:
    - echo "Deploying to production server..."
    - ssh gpao@10.13.112.161 "docker pull $DOCKER_REGISTRY/$IMAGE_NAME:v$(cat $VERSION_FILE)
        && docker stop gpao_main_api || true && docker rm gpao_main_api || true
        && docker run -d --name gpao_main_api $DOCKER_REGISTRY/$IMAGE_NAME:v$(cat $VERSION_FILE)"
  when: manual  # Manual trigger for deployment to prod
  only:
    - master
  environment:
    name: prod
    url: https://erp.futurmap.local
```

Cette étape déploie l'image sur le serveur de production :
- Elle appartient à la phase `deploy_prod`.
- Le processus est similaire à celui des déploiements précédents, mais sur le serveur de production.
- Cette étape est également déclenchée manuellement (`when: manual`).
- Elle ne s'exécute que sur la branche `master`.
- Elle définit l'environnement `prod` avec une URL spécifique.
