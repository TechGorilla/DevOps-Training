# Challenge 7 - Jenkins with Docker agents  
---

Date limite de remise du challenge: le 07 mars 2021 

# Task 1: Accessing Docker from a Dockerized Jenkins
Durée estimée : **Lecture**: 30 minutes **Réalisation**: 2 Heures

L'objectif ici est de comprendre et d'implémenter les alternatives possible pour l'accès à un engine Docker à partir d'un Jenkins Dockerisé. Tiu Wee Han, dans son article [Docker in Jenkins in Docker](https://www.tiuweehan.com/blog/2020-09-10-docker-in-jenkins-in-docker/) explique et illustre les 3 solutions suivantes:
- Solution 1: Build a new Jenkins image with Docker installed.
- Solution 2: Mount the host's docker unix socket onto the Jenkins container.
- Solution 3: Run the official docker-in-docker image and expose its TCP socket to the Jenkins. container

Travail demandé: 
 - Implémenter et tester les 3 solutions ci-dessus et discuter de leurs avantages et inconvénients.
 - Lire, implementer, et tester le [HOW-TO officiel d'installation de Jenkins sous Docker](https://www.jenkins.io/doc/book/installing/docker/). Dédiure laquelle parmi les trois solutions indiquées ci-dessus se trouve-t-elle prévilégiée par l'opinion de Jenkins officiel. 


# Task 2: Jenkins Distributed builds with Docker agents
Durée estimée : **Katacoda:** 15 à 20 minutes. **Mise en place de l'agent:** 2 Heures

L'objectif ici est de configurer des builds Jenkins distribués dans lesquels Docker joue le rôle d'agent.
 - Suivre et appliquer les instructions du Katacoda [Building Docker Images using Jenkins](https://www.katacoda.com/courses/cicd/build-docker-images-using-jenkins). Ce Katacoda illustre l'utilisation d'un agent Docker pour construire une image Docker. Ce Katacoda  est intéressant dans la mesure où illustre le processus global de mise en place d'un agent Docker, il montre surtout comment configurer le Plugin Docker (step 2 du Katacoda) et comment ajouter l'agent (step 3 du Katacoda) au master Jenkins. Toutefois, l'environnement de l'agent est deja pré-configuré par l'auteur.
 - Construire par vous même un environnement d'agent Docker pour Jenkins. Suivre le tutorial suivant, par Ashu Rana, sur Medium, intitulé : [Using Docker Containers as Jenkins Build Slaves](https://medium.com/xebia-engineering/using-docker-containers-as-jenkins-build-slaves-a0bb1c9190d). Les mêmes idées sont également développées par Bibin Wilson, dans son article [How to Setup Docker Containers as Build Slaves for Jenkins](https://devopscube.com/docker-containers-as-build-slaves-jenkins/). L'auteur fournit une [vidéo Youtube explicative](https://youtu.be/yb6DodK6mbg). 
