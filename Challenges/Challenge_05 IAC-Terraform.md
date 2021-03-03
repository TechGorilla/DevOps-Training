# Challenge 5 - Infrastructure As Code with Terraform 
---

Date limite de remise du challenge: le 20 février 2021 

# Task 1: Using Terraform modules
L'objectif ici est de comprendre le principe des modules Terraform et de les mettre en application. 

Travail demandé: 
 - Implémenter les étapes du tutoriel de démarrage avec Terraform modules de Hashicorp se trouvant [ici](https://learn.hashicorp.com/collections/terraform/modules). Ce tutoriel comprend 6 étapes et requiert 1heure 9minutes. Si besoin, vous pouvez trouvez d'autres ressources sur les modules de Terraform : La [section sur les modules](https://app.pluralsight.com/course-player?clipId=694f2109-1579-4d9c-9fb6-3e4f9e96a574) du cours Pluralsight : Terraform, getting started. Les clips Youtube suivants : [How to Create AWS VPC with Terraform Modules](https://www.youtube.com/watch?v=5-0bAfZd7SY,https://www.youtube.com/watch?v=5-0bAfZd7SY),  [Modules | Terraform Tutorial | #15](https://www.youtube.com/watch?v=7jnuTdhxjhw), et [Azure Fundamentals - #22 - Azure Bastion](https://youtu.be/Ixl44IRkxj0).
 - Déployer le module Terraform de démonstration d'une application Nginx sur une instance EC2 d'AWS. Voici son [URL](https://registry.terraform.io/modules/codygreen/nginx-demo-app/aws/latest). Sur cet URL vous trouverez le lien GitHub du module ainsi que les 'Provision instructions' : <https://github.com/codygreen/terraform-aws-nginx-demo-app>. A titre de remarque, ce module n'a qu'une seule variable obligatoire, celle nommée `ec2_key_name`, de type `string` et qui représente le nom de la clé AWS EC2 pour l'accès SSH. 

  
# Task 2: Implementing Bastion Hosts
L'objectif ici est de comprendre et de mettre en application le pattern _Bastion Host_, connu également sous le nom _Jump Box_. 

Travail demandé:
- Lire l'article [AWS Bastion Host - How to create it?](https://www.knowledgehut.com/tutorials/aws/aws-bastion-hostVisualiser) afin de s'introduire au concept de Bastion Host. Visualier les séquences Youtube suivantes qui expliquent également ce concept :  <https://www.youtube.com/watch?v=pNE9J81aYLc&list>, <https://www.youtube.com/watch?v=VJjy7AH3J9Q>, <https://youtu.be/Mwf17O45IA0> .
   
- Procéder à la mise en place d'un host bastion pour un VPC AWS en suivant l'article _Connecting to an ec2 instance in a private subnet on AWS_ qui se trouve [ici](https://towardsdatascience.com/connecting-to-an-ec2-instance-in-a-private-subnet-on-aws-38a3b86f58fb). Le code du projet  terraform relatif à cet article est disponible sur ce GitHub :  <https://github.com/HDaniels1991/AWS-Bastion-Host/>
