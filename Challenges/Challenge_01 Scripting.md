
# Challenge 1 - Scripting

# Enoncé du challenge
On utilise des VM Ubuntu 18.04 managées par Vagrant et on vous demande d'écrire un Script de provisionnement des outils pour une station de station de travail de développeurs d'applications.   
Il y a deux catégories de développeurs :  
 
1. Développeurs Nodejs
2. Développeurs Java.

Le script à écrire installera les outils requis lors de la création de VM par Vagrant. On retient l'option Bash Shell pour rédiger ce script. Ensuite, on produira optionnellement une version Python, en version 3.6.9, version installée par défaut et qui est considérée suffisante ici.

# Indications
Pour vous illustrer comment intégrer le script de provionnement dans la configuration de Vagrant (i.e.le Vagrantfile), voici un petit prototype que je viens de mettre en place te tester. Il provisionne nginx avec un scipt bash appelé provision-nginx.sh. Vous remarquez que j'ai configuré un port forward du port par défaut de nginx (80) vers le port 8080 du Host Windows afin d'accéder à mon nginx depuis mon Host.

Le contenu littéral du Vagrantfile est le suivant: 

```bash
Vagrant.configure("2") do |config|
     config.vm.box = "ubn untu/bionic64"
     config.vm.hostname = "dev-static-nginx"
     config.vm.network :forwarded_port, guest: 80, host: 8080
     config.vm.synced_folder "./web", "/var/www/html", type: "virtualbox"    
     config.vm.define "dev-static-nginx"
     config.vm.provider "virtualbox" do |vb|
           vb.name = "dev-static-nginx"  
           vb.memory = "1024"  # 1GB Memory
           vb.cpus = 1  # 1 CPU
     end
     config.vm.provision "shell", path:"provision-nginx.sh"  
 end 
```

Pour tester l'installation de mon nginx, j'ai déployé sur Nginx un micro site web composé de la page index.html se trouvant sous le dossier web. Les choses marchent et voici le rendu de mon site.

# Travail demandé :

Un rendu challenge-1-dev-java, ou bien un challenge-2-dev-nodejs en version Bash et si vous aurez le souffle en version Python.

## Task 1 
Créer un projet Vagrant composé d'un Répertoire incluant le Vagrantfile, le script de provisionnement et les autres éventuels artéfacts utiles pour le projet.
Vous choisissez l'une parmi les deux cibles: développeurs Nodejs ou bien développeurs Java. Voici dans chaque cas le cahier des charges des outils requis.

- Outils pour l'environnement de développement Java : Java JDK 11 (OpenJDK), Apache Maven 3,  MySQL (ou MariaDB  8, Apache Tomcat 9, Angular CLI, et Docker Community Edition.

- Outils pour l'environnement de développement Nodejs : Nodjs et le NPM associé, nginx, MongoDB_, Angular CLI, et Docker Community Edition.

## Solution 1

### Environnement de développement Java (BASH)

##### Arborescence

````sh
challenge-1-dev-java/
      Vagrant/
            - provision-jdk-apache.sh
            - provision-angular.sh
            - Vagrantfile
````

##### provision-jdk-mysql-apache script
````sh
#!/bin/bash
sudo apt-get update && sudo apt-get install -y\
>openjdk-11-jdk\
>maven\
>mysql-server\
>tomcat9
echo "JAVA - MAVEN - MYSQL - TOMCAT installed"
````
##### provision-angular script
````sh
#!/bin/bash
sudo apt install python-software-properties
curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
sudo apt install -y nodejs
sudo apt install -y build-essential
sudo npm install -g @angular/cli
echo "NODE installed"
````
##### grant execute permissions
````sh
# All are granted execute permission, group can read, user has all rights.
Vagrant$ sudo chmod 751 provision-jdk-mysql-apache.sh
Vagrant$ sudo chmod 751 provision-angular.sh
````

##### Vagrantfile
````ruby
Vagrant.configure("2") do |config|
     config.vm.box = "ubuntu/bionic64"
     config.vm.hostname = "dev-static-nginx"
     config.vm.network :forwarded_port, guest: 80, host: 8080
     config.vm.synced_folder "./web", "/var/www/html", type: "virtualbox"    
     config.vm.define "dev-static-nginx"
     config.vm.provider "virtualbox" do |vb|
           vb.name = "dev-java"  
           vb.memory = "2048"  # 2GB Memory
           vb.cpus = 1  # 1 Cpu
     end
     config.vm.provision "shell", path:"provision-jdk-mysql-apache.sh"
     config.vm.provision "shell", path:"provision-angular.sh"
     config.vm.provision : docker
 end 
````


### Environnement de développement NodeJS (PYTHON)

##### Arborescence

````sh
challenge-2-dev-nodejs/
      Vagrant/
            - provision-nginx.py
            - provision-mongodb.py
            - provision-nodejs-angular.py
            - Vagrantfile
````
##### provision-nginx script
````python
#!/usr/bin/env python3
import subprocess 
p1 = subprocess.run('sudo apt-get update && sudo apt-get install -y nginx',capture_output=True, text=True, shell=True)
p1()
print(p1.stdout.decode())
````

##### provision-nodejs-angular script
````python
#!/usr/bin/env python3
import subprocess
p2 = subprocess.run('curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -', capture_output=True, text=True, shell=True)
p3 = subprocess.run('sudo apt install -y nodejs build-essential', capture_output=True, text=True, shell=True)
p4 = subprocess.run('npm install -g @angular/cli', capture_output=True, text=True, shell=True)
p2()
p3()
p4()
print(p2.stdout.decode())
print(p3.stdout.decode())
print(p4.stdout.decode())
````

##### provision-mongodb script
````python
#!/usr/bin/env python3
import subprocess 
p5 = subprocess.run('wget -qO - https://www.mongodb.org/static/pgp/server-4.4.asc | sudo apt-key add -',capture_output=True, text=True, shell=True)
p6 = subprocess.run('echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu bionic/mongodb-org/4.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.4.list',capture_output=True, text=True, shell=True)
p7 = subprocess.run('sudo apt-get update -y && sudo apt-get install -y mongodb-org', capture_output=True, text=True, shell=True)
p5()
p6()
p7()
print(p5.stdout.decode())
print(p6.stdout.decode())
print(p7.stdout.decode())
````

##### Vagrantfile
````ruby
Vagrant.configure("2") do |config|
     config.vm.box = "ubuntu/bionic64"
     config.vm.hostname = "dev-static-nginx"
     config.vm.network :forwarded_port, guest: 80, host: 8080
     config.vm.synced_folder "./web", "/var/www/html", type: "virtualbox"    
     config.vm.define "dev-static-nginx"
     config.vm.provider "virtualbox" do |vb|
           vb.name = "dev-nodejs"  
           vb.memory = "2048"  # 2GB Memory
           vb.cpus = 1  # 1 Cpu
     end
     config.vm.provision "shell", inline: "python3 provision-nodejs-angular.py"
     config.vm.provision "shell", path:"python3 provision-nginx.py"
     config.vm.provision "shell", path:"python3 provision-mongodb.py"
     config.vm.provision : docker
 end 
````

## Task 2
Se connecter sur la VM et vérifier que les installations ont bien réussi.

## Solution 2

````sh
# créer la VM et la provisionner
/Vagrant$ vagrant up 
# se connecter sur la VM 
/Vagrant$ vagrant ssh
# vérifier les installations en utilisant l'attribut "version"
/vagrant$ *package* --version
````
