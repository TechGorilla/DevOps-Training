# Challenge 3 - Loadbalancing VMs and Containers with NGinx

Date limite de remise du travail :  Le 18/01/2021 à 00H. Le livrable doit être pushé sur votre dépôt personnel GitHub/GitLab en m'envoyant une invitaion me donnant le droit d'y accéder.

# Objectifs du challenge : 
- S'initier aux fonctionnalités de Nginx : loadbalancing, SSL
- Savoir configurer une infra à base VM ainsi que son équivalent à base de Conteneurs Docker
- Configurer TLS/SSL sur Nginx

# Travail demandé

## Task 1
Configurer une infrastructure composée de 3 VMs Linux. 1 VM jouera le rôle du loadbalanceur Nginx et les deux autres joueront le rôle d'agents backends. Utiliser Hashicorp Vagrant pour créer toute l'infra et son provisionnement.
- Estimation de la durée de réalisation : 1 heure.
- Indications : [Lien 1](https://youtu.be/SpL_hJNUNEI),  [Lien 2](https://redgreenrepeat.com/2018/06/01/nginx-load-balancer-configuration/)

### Solution 1

#### Vagrantfile

````bash
OX_IMAGE = "ubuntu/xenial64"
BALANCER_IP_ADDRESS = "192.168.100.10"
BALANCER_PORT = 8081

Vagrant.configure("2") do |config|
 config.vm.box = BOX_IMAGE

  config.vm.define "balancer" do |subconfig|
    subconfig.vm.box = BOX_IMAGE
    subconfig.vm.hostname = "balancer"
    subconfig.vm.network "forwarded_port", guest: 80, host: BALANCER_PORT
    subconfig.vm.network :private_network, ip: BALANCER_IP_ADDRESS
    subconfig.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y nginx
      echo "#{subconfig.vm.hostname}" > /var/www/html/index.nginx-debian.html
      service nginx start
    SHELL
  end
end

BOX_IMAGE = "ubuntu/xenial64"
BALANCER_IP_ADDRESS = "192.168.100.10"
BALANCER_PORT = 8081
WORKER_COUNT = 2

Vagrant.configure("2") do |config|
 config.vm.box = BOX_IMAGE

  config.vm.define "balancer" do |subconfig|
    subconfig.vm.box = BOX_IMAGE
    subconfig.vm.hostname = "balancer"
    subconfig.vm.network "forwarded_port", guest: 80, host: BALANCER_PORT
    subconfig.vm.network :private_network, ip: BALANCER_IP_ADDRESS
    subconfig.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y nginx
      echo "#{subconfig.vm.hostname}" > /var/www/html/index.nginx-debian.html
      service nginx start
    SHELL
  end

  (1..WORKER_COUNT).each do |worker_count|
    config.vm.define "worker#{worker_count}" do |subconfig|
      subconfig.vm.box = BOX_IMAGE
      subconfig.vm.hostname = "worker#{worker_count}"
      subconfig.vm.network "forwarded_port", guest: 80, host: (BALANCER_PORT + worker_count)
      subconfig.vm.network :private_network, ip: "192.168.100.#{10 + worker_count}"
      subconfig.vm.provision "shell", inline: <<-SHELL
        apt-get update
        apt-get install -y nginx
        echo "#{subconfig.vm.hostname}" > /var/www/html/index.nginx-debian.html
        service nginx start
      SHELL
    end
  end

  config.vm.provider "virtualbox" do |vb|
    # Customize the amount of memory on the VM:
    vb.memory = "2048"
  end
end
````

#### NGINX Configuration

````conf
 upstream backend {
   server 192.168.100.11;
   server 192.168.100.12;
   server 192.168.100.13;
 }

upstream even {
  server 192.168.100.12;
}

upstream odd {
  server 192.168.100.11;
  server 192.168.100.13;
}

server {
  listen 80;

  location /odd/ {
    rewrite ^/odd(.*) /$1 break;
    proxy_pass http://odd;
  }

  location /even/ {
    rewrite ^/even(.*) /$1 break;
    proxy_pass http://even;
  }

  location /worker {
    rewrite ^/worker(.*) /$1 break;
    proxy_pass http://backend;
  }
}
````



## Task 2
Recréér l'infrastructure de la Task 1 en utilisant, cette fois ci, des conteneurs Docker. Il faut donc 3 conteneurs, l'un jouant le rôle de loadbalanceur et les deux autres le rôles d'agents de backend.  Faire le travail, dans une première étape, sans docker-compose, puis le faire avec docker-compose.
- Estimation de la durée de réalisation : 1 heure.
- Indications :  [Lien](https://www.bogotobogo.com/DevOps/Docker/Docker-Compose-Nginx-Reverse-Proxy-Multiple-Containers.php)

### Solution 2

#### Dockerfile

````docker
FROM nginx:alpine
COPY nginx.conf /etc/nginx/nginx.conf
RUN apk update && apk add bash
````

#### reverseproxy Image Build

````bash
$ docker build -t reverseproxy .
````

#### NGINX Configuration

````conf
worker_processes 1;
  
events { worker_connections 1024; }

http {

    sendfile on;

    upstream docker-nginx {
        server nginx:80;
    }

    upstream docker-apache {
        server apache:80;
    }
    
    proxy_set_header   Host $host;
    proxy_set_header   X-Real-IP $remote_addr;
    proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header   X-Forwarded-Host $server_name;
    
    server {
        listen 8080;
 
        location / {
            proxy_pass         http://docker-nginx;
            proxy_redirect     off;
        }
    }
 
    server {
        listen 8081;
 
        location / {
            proxy_pass         http://docker-apache;
            proxy_redirect     off;
        }
    }
}

````

#### docker-compose

````yml
services:
    reverseproxy:
        image: reverseproxy
        ports:
            - 8080:8080
            - 8081:8081
        restart: always
 
    nginx:
        depends_on:
            - reverseproxy
        image: nginx:alpine
        restart: always
 
    apache:
        depends_on:
            - reverseproxy
        image: httpd:alpine
        restart: always
````

## Task 3
Ajouter la fonctionnalité SSL avec des clés auto-signés au noeud Loadbalanceur NGinx
- Estimation de la durée de réalisation : 1 heure
- Indications :  [Lien 1](https://youtu.be/X3Pr5VATOyA), [Lien 2](https://www.johnmackenzie.co.uk/posts/using-self-signed-ssl-certificates-with-docker-and-nginx/)

### Solution 3

#### Directory structure and files


````bash
- ssl-docker-nginx/
   - nginx
     - logs/
       - my-site.com.access.log
     - nginx.conf
   - site/
     - index.html
   - docker-compose.yml
````

#### NGINX Configuration

````conf
events {
  worker_connections  4096;  ## Default: 1024
}

http {
    server {
        listen 80;
        server_name my-site.com;
        root         /usr/share/nginx/html/;
    }
}
````

#### docker-compose

````yml
version: '2'
services:
  server:
    image: nginx:1.15
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf
      - ./site:/usr/share/nginx/html
    ports:
    - "8080:80"
````

#### Add my-site.com to /etc/hosts

Edit /etc/hosts

````bash
sudo nano /etc/hosts
````

Add this line to file and save

````nano
0.0.0.0    my-site.com
````

#### Create self-signed SSL certificate

Creating key/cert pair
````bash
openssl req -newkey rsa:2048 -nodes -keyout nginx/my-site.com.key -x509 -days 365 -out nginx/my-site.com.crt
````
Mounting key/cert pair to container : edit docker-compose.yml

````yml
version: '2'
services:
  server:
    image: nginx:1.15
    volumes:
      - ./site:/usr/share/nginx/html
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf
      - ./nginx/my-site.com.crt:/etc/nginx/my-site.com.crt # New Line!
      - ./nginx/my-site.com.key:/etc/nginx/my-site.com.key # New Line!
    ports:
    - "8080:80"
 ````
 
 Opening port 443 on nginx container : edit docker-compose.yml
 
 ````yml
 version: '2'
services:
  server:
    image: nginx:1.15
    volumes:
      - ./site:/usr/share/nginx/html
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf
      - ./nginx/my-site.com.crt:/etc/nginx/my-site.com.crt
      - ./nginx/my-site.com.key:/etc/nginx/my-site.com.key
    ports:
    - "8080:80"
    - "443:443" # Signals docker to start listening on 443, and redirect to 443
````

#### Configuring NGINX to serve my-site.com over https using the self signed certificate

Edit nginx.conf

````conf
events {
  worker_connections  4096;  ## Default: 1024
}

http {
    server {
        listen 80;
        server_name my-site.com;
        root         /usr/share/nginx/html;
    }

    server { # This new server will watch for traffic on 443
        listen              443 ssl;
        server_name         my-site.com;
        ssl_certificate     /etc/nginx/my-site.com.crt;
        ssl_certificate_key /etc/nginx/my-site.com.key;
        root        /usr/share/nginx/html;
    }
}
````


 
 
 
