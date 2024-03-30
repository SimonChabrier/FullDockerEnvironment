# Environnement LAMP complet Dockerisé avec MySql PhpMyAdmin Mailhog Php et Apache
## Ok pour faire tourner des projet symfony 6 et symfony 7

à la racine du projet global : 

dossiers impératifs :
- apache (la config apache de base pour pinter vers le rep phpinfos et afficher les infos php du container server sur le port : http://localhost:8001)
- php (les scripts à executer pour créer le container server -> install composer -> symfony CLI etc)
- server (c'est le "serveur" - le contenu de var/www est accéssible directement dans le dossier local server)

pour buider les containers la première fois ou rebuilder après modif de config.
- ```docker-compose up --build``` 

si les containers existent déjà directement
- ```docker-compose up``` 
(si ils n'existent pas ils seront buid de toute façon avant d'être lancés).

! si on build uniquement alors il faut faire up ensuite pour lancer les containers !

IMPORTANT -> Toujours travailler dans le container server directement.
- /var/www du container === /server en local (/var/www === ./server)

- récupèrer l'id du container server sur docker desktop et lancer le terminal du server. (lancer un terminal pour gèrer le serveur et un autre pour passer les commandes).
- ```docker exec -it ID_CONTAINER /bin/bash```
- exemple complet :
- ```docker exec -it 2d14c4411e83f83495ad3567d01de4e6aac0a20897a33ccd3ab92326d60ffeab /bin/bash```

- dans le terminal on est maintenant dans le container "server" dans var/www on a donc les CLI et Composer (comme si on était sur un serveur c'est la même idée).

- Pour lancer un application symfony déjà installée
- ```ls```pour voir le contenu de var/www
- ```cd nom_de_application```
- ```symfony serve``` pour lancer l'application.
  
- installer une application dans le dossier /var/www du container server: 
- ```symfony new app --version="7.0.*" --webapp```

- config Doctrine dans le .env ou .env.local 
- on met le nom utilisateur et le mot de passe renseignés dans docker-compose.yaml 
- pour la connexion on met le nom du service renseignés dans docker-compose.yaml: Eg: database puis qu'il redirige vers les ports adaptés.
 
- ```DATABASE_URL="mysql://symfony:symfony@database/symfony_docker?serverVersion=8.0.32&charset=utf8mb4"```

Ensuite on travaille normalement..on peut supprimer le dossier app et refaire une install etc

Ne pas supprimer ni renommer le dossier symfony sans changer la configuration de docker ici :  
- ```./server:/var/www``` par ```./nouveau_nom:/var/www``` et refaire un build pour mettre à jour le/les container.

pour le https sur le serveur dans le container de l'app on installe (il y a un avertissement navigateur mais c'est pas grave c'est juste informatif):
- ```symfony server:ca:install```
- c'est parce que le certificat SSL est autosigné il suffit d'ajouter une exception au navigateur pour https://127.0.0.1:8000/ et c'est ok.

- La config apache est utilisée uniqument pout afficher les infos php ensuite les app symfony utilise le serveur interne de symfony dans pas besoin de apache.