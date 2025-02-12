# Accès au serveur
- Dev: ssh gpao@10.13.112.173
- Preprod: ssh gpao@10.13.112.152
- Prod: ssh gpao@10.13.112.161

NB: le mot de passe est: Gp5894

# Application web ERP
- Dev:  https://dev-gpao.futurmap.local/login
- Preprod: https://staging-erp.futurmap.local/
- Prod: https://erp.futurmap.local/accueil
# Accès à la base de données
ERP
- Name: gpao_prod
- User: gpao_pg
- Password: aze/*-123
- Host: 10.13.112.161 (PROD)
- Port: 5432

ANALYSE
- Name: gpao_analyse_prod
- User: gpao_pg
- Password: aze/*-123
- Host: 10.13.112.161 (PROD)
- Port: 5432

Ou bien utiliser cette url pour acceder au pgadmin web local
- URL: http://10.13.112.161:8888/
- Login: franco@futurmap.com
- Pass: aze/*-123


# Gitlab local
Il faut créer un compte dans cette url: http://10.13.112.154/ 

# Liste projets dans prod

### Backend ERP:

    - Dir
    **cd /home/gpao/apps/gpao_main_api**

    - Redemarrer le service docker 
    **docker compose -f docker-compose-prod.yml up -d --build**


### Frontend ERP

    - Dir
    **cd /var/www/html/gpao-front**

    - Redemarrer le service nginx 
    **sudo service nginx restart**


### Analyse 

    - Dir
    **cd /home/gpao/apps/gpao_routine**

    - Basculer vers la variable d'environnement
    **source venv/bin/activate**

    - Lancer un parametre dans le main
    **python main.py -atpj** (pour charger la table 'fait_rh_temps_passse_journalier' par exemple)


### Export 
A chaque bug de l'export il faut lancer cette commande
  **docker restart node_app_2**


### Fgame
    - Dir
    **cd /home/gpao/apps/fgame_main_api**

    - Build
    **docker build -t fgame_main_api .**

    - Run with volume
    **docker run -d -p 3343:3343 -p 3333:3333 -v /home/gpao/apps/fgame/fgame_main_api/media:/code/media -v /etc/ssl/nginx/:/etc/ssl/nginx/ --name fgame_main_api fgame_main_api**



# Modifier un horaire exceptionnel dans la base
Utiliser cette requete pour gerer l'horaire exceptionnel dans la base et modifier les données dans le pgadmin

**SELECT ex.* FROM mod_rh_user_horaire_exception_shift ex JOIN mod_rh_user_profile up ON up.user_ptr_id = ex.user_id WHERE up.matricule = 'xxx' ORDER BY ex.date_debut DESC**

    # NB: xxx: Matricule qu'on va gerer


