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

```bash
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
```

#### NGINX Configuration

```
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
```



## Task 2
Recréér l'infrastructure de la Task 1 en utilisant, cette fois ci, des conteneurs Docker. Il faut donc 3 conteneurs, l'un jouant le rôle de loadbalanceur et les deux autres le rôles d'agents de backend.  Faire le travail, dans une première étape, sans docker-compose, puis le faire avec docker-compose.
- Estimation de la durée de réalisation : 1 heure.
- Indications :  [Lien](https://www.bogotobogo.com/DevOps/Docker/Docker-Compose-Nginx-Reverse-Proxy-Multiple-Containers.php)

### Solution 2

#### Dockerfile

```docker
FROM nginx:alpine
COPY nginx.conf /etc/nginx/nginx.conf
RUN apk update && apk add bash
```

#### Reverse Proxy Immage

```bash
$ docker build -t reverseproxy .
```



## Task 3
Ajouter la fonctionnalité SSL avec des clés auto-signés au noeud Loadbalanceur NGinx
- Estimation de la durée de réalisation : 1 heure
- Indications :  https://youtu.be/X3Pr5VATOyA, https://www.johnmackenzie.co.uk/posts/using-self-signed-ssl-certificates-with-docker-and-nginx/


