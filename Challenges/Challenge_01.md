Enoncé du challenge:
====================
On utilise des VM Ubuntu 18.04 managées par Vagrant et on vous demande d'écrire un Script de provisionnement des outils pour une station de station de travail de développeurs d'applications.   
Il y a deux catégories de dévloppeurs :  
 
1. Développeurs Nodejs
2. Développeurs Java.

Le script à écrire installera les outils requis lors de la création de VM par Vagrant. On retient l'option Bash Shell pour rédiger ce script. Ensuite, on produira optionnellement une version Python, en version 3.6.9, version installée par défaut et qui est considérée suffisante ici.

Travail demandé :  

1- Créer un projet Vagrant composé d'un Répertoire incluant le Vagrantfile, le script de provisionnement et les autres éventuels artéfacts utiles pour le projet.
Vous choisissez l'une parmi les deux cibles: développeurs Nodejs ou bien développeurs Java. Voici dans chaque cas le cahier des charges des outils requis.

- Outils pour l'environnement de développement Java : Java JDK 11 (OpenJDK), Apache Maven 3,  MySQL (ou MariaDB  8, Apache Tomcat 9, Angular CLI, et Docker Community Edition.

- Outils pour l'environnement de développement Nodejs : Nodjs et le NPM associé, nginx, MongoDB_, Angular CLI, et Docker Community Edition.

2- Se connecter sur la VM et vérifier que les installations ont bien réussit.

Indications
-----------
Pour vous illustrer comment intégrer le script de provionnement dans la configuration de Vagrant (i.e.le Vagrantfile), voici un petit prototype que je viens de mettre en place te tester. Il provisionne nginx avec un scipt bash appelé provision-nginx.sh. Vous remarquez que j'ai configuré un port forward du port par défaut de nginx (80) vers le port 8080 du Host Windows afin d'accéder à mon nginx depuis mon Host.

Le contenu littéral du Vagrantfile est le suivant: 

```bash
Vagrant.configure("2") do |config|
     config.vm.box = "ubuntu/bionic64"
     config.vm.hostname = "dev-static-nginx"
     config.vm.network :forwarded_port, guest: 80, host: 8080
     config.vm.synced_folder "./web", "/var/www/html", type: "virtualbox"    
     config.vm.define "dev-static-nginx"
     config.vm.provider "virtualbox" do |vb|
           vb.name = "dev-static-nginx"  
           vb.memory = "1024"  # 1GB Memory
           vb.cpus = 1  # 1 Cpu
     end
     config.vm.provision "shell", path:"provision-nginx.sh"  
 end 
```

Pour tester l'installation de mon nginx, j'ai déployer sur Nginx un micro site web composé de la page index.html se trouvant sous le dossier web. Les choses marchet et voici le rendu de mon site.

Travail demandé :
-----------------
Un rendu challenge-1-dev-java, ou bien un challenge-2-dev-nodejs en version Bash et si vous aurez le souffle en version Python.