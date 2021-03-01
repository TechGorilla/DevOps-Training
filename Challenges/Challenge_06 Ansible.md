# Challenge 6 - Ansible with Docker 
---

Date limite de remise du challenge: le 27 février 2021 

# Task 1: Building an Ansible Docker Container
Durée estimée : 30 minutes

L'objectif ici est de produire une image Docker contenant le software Ansible. Cette image est surtout utile pour faire de l'Ansible à partir de Windows.  

Travail demandé: 
 - Builder le Dockerfile suivant pour produire une image locale. Démarrer un conteneur à partir de cette image et vérifier que Ansible est bien opérationnel. Vérifier que l'image contient bien SSH et python.
```shell
FROM ubuntu:18.04
RUN apt-get update && \
    apt-get install --no-install-recommends -y software-properties-common && \
    apt-add-repository ppa:ansible/ansible && \
    apt-get update && \
    apt-get install -y ansible
RUN echo '[local]\nlocalhost\n' > /etc/ansible/hosts
```

# Task 2: Ansible Lab environment using Docker
Durée estimée : 1 heure.

L'objectif ici est de construite un Environnement de développement Ansible à base de conteneurs Docker. On se réfère à l'artile de LMtx se trouvant ici: https://morioh.com/p/fa458766759f. L'auteur fournit le lien au GitHub contenant le code source de l'article.

Suivre les instructions du Quick start de l'article et prendre en compte les notes suivantes:

- **Note 1**: Après avoir clôné le dépôt GitHub de l'auteur, corrigez la version de l'image `ubuntu` pour la positionner sur la `18.04`. Cette modification sera faite sur le `Dockerfile` présent dans le dossier `ansible/base`. La ligne `FROM` doit donc ressembler à la suivante:
```shell
FROM ubuntu:18.04
```
- **Note 2**: En essayant cet example, j'ai rencontré la situation indiqué par l'auteur dans la section **Troublshooting** se trouvant à la fin de son article. En effet, les hosts s'éteignaient dès leur création. Pour outrepasser ce problème, il faut cahnger l'encodage des fins de ligne du fichier  `ansible/hosts/run.sh` qui doit être **Linux/Unix (LF)** et non pas  **Windows (CRLF)**. Vous pouvez facilement changer ces "end of line"  avec votre editor Visual Studio Code ou bien Notepad++.
