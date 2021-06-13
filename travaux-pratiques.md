# Règles

Vous avez les choix entre ces deux propositions de TP.
La seconde est plus compliquée et je serai donc plus tolérant à certaines erreurs pour remercier ceux/celles qui se lancent dessus.
Néanmoins vous ne serez pas mal noté si vous choisissez la première.
Vous ne partez pas tous avec la même expérience donc j'estime normal de vous donner le choix. Faites vous plaisir en fonction du temps aussi que vous avez.

Choisissez une application quelqueconque dans votre langage favori qui affiche un "C'était le 26 mai 93" en page web et aussi sur la sortie standard.
L'application sait lire une configMap (même namespace forcèment) pour afficher le nom de buteur. Je vous laisse chercher sur Google cette joli référence.
Vous ne serez pas noter sur le CSS. Le but est de sortir du texte et du HTML brut en respectant les https://12factor.net/fr/.
Vous développez donc le Dockerfile adéquat pour builder cette application.
L'image sera poussée sur le DockerHub sur une compte que vous allez créer pour ce TP (à moins que vous en ayez un déjà).
Les crédentials pour le DockerHub seront à mettre dans des variables Gitlab-CI.

# Cluster K3S

Utiliser le TP suivant pour le provisionner sur votre poste https://github.com/zepouet/montpellier.univ/blob/main/multipass-k3s.md
OU même K3D si vous voulez. https://k3d.io/

# Livrables

* Un dépôt Gitlab contenant le code mais aussi un fichier ARCHITECTURE.md expliquant succintement vos choix avec des captures d'écran.
* Une démo prouvant que ceci fonctionne bien si vous le souhaitez pas obligatoire. Soyez par contre plus prévis dans les explications du fichier ARCHITECTURE.md

Vous ne serez pas noté sur la quantité de texte écrite. Soyez succint et précis.

# PROPOSITION 1

Contexte : Un développeur doit fournir une solution cloud native clef en main.
Il est seul à développer donc pas de souci niveau droit à se partager entre personnes.

* Créer un utilisateur prefixe-dev (nous prendrons xxx-dev pour exemple)
* Créer un dépots PROJET dans Gitlab
  * PROJET : contenant le code source PHP, Node... Java... Python... Scala pour faire plaisir à votre responsable d'unité.
    * Droits MAINTENER pour xxx-dev
    * Contient les fichiers suivants *a minima* :
      * .gitlab-ci.yaml
      * Dockerfile 
      * Service Account en Variable masquée 

## Remarques : 
* Créer un service account limitée au namespace avec accès total pour POD/REPLICASET/DEPLOYMENT/CONFIGMAP uniquement. Pour réaliser ceci vous devvez livrer un fichier explicitant l'ensemble des commandes que vous devez jouer en étant admin du cluster K8S. Forcèment pour avoir le droits de créer des SA, il faut être les droits nécessaires.
* Ne pas utiliser DinD (Docker in Docker) pour builder l'image Docker dans .gitlab-ci. Choisir entre Buildah, Kaniko ou en utilisant la Socket Docker dans un runner même si cette solution la moins sécurisée est très pertinente dans une infra non mutualisée, non exposée à des tiers.
* Bien faire attention à pousser les logs de votre application sur la sortie standard.
* Exposer l'application sur un Ingress au choix (Nginx, Traefik, HaProxy...) sur l'URL **exam.do.local** (Spoofer l'IP dans /etc/hosts) sur du HTTP.
 
# PROPOSTION 2

Contexte : similer le fait d'avoir des utilisateurs différentes qui ont des droits différents.
Typiquement une agence web pour laquelle vous allez mettre un dépôt avec les droits DEVELOPPER.
 
* Créer deux utilisateurs prefixe-dev et prefixe-ops (nous prendrons xxx-dev et xxx-ops pour exemple)
* Créer deux dépots DEV / OPS dans Gitlab
  * DEV : contenant le code source PHP, Node... Java... Python... Scala pour faire plaisir à votre responsable d'unité.
    * Droits DEVELOPER pour xxx-dev
    * Droits MAINTENER pour xxx-ops
  * OPS 
    * Droits READER pour xxx-dev
    * Droits MAINTENER pour xxx-ops
    Contient les fichiers suivants *a minima* :
      * .gitlab-ci.yaml
      * Dockerfile 
      * Service Account en variable masquée 

## Remarques : 
* Créer un service account limitée au namespace avec accès total pour POD/REPLICASET/DEPLOYMENT/CONFIGMAP uniquement. Pour réaliser ceci vous devvez livrer un fichier explicitant l'ensemble des commandes que vous devez jouer en étant admin du cluster K8S. Forcèment pour avoir le droits de créer des SA, il faut être les droits nécessaires.
* Ne pas utiliser DinD (Docker in Docker) pour builder l'image Docker dans .gitlab-ci. Choisir uniquement entre Buildah et Kaniko. De même il est interdit d'utiliser la Socket Docker car environnement mutualisée possible. Solution pas sécurisée.
* Bien faire attention à pousser les logs de votre application sur la sortie standard. 
* Exposer l'application sur un Ingress au choix (Nginx, Traefik, HaProxy...) sur l'URL **exam.do.local** (Spoofer l'IP dans /etc/hosts) sur du HTTP.

## Extra 
 
Tout n'est pas à faire sauf si vous en rêvez.
 
* Mettre en place un Vault pour éviter de stocker le ServiceAccount en tant que variable masquée dans Gitlab. Choix possible entre mozilla SOPS, Hashicorp Vault ou même https://github.com/bitnami-labs/sealed-secrets. 
* Déployer une solution comme https://banzaicloud.com/docs/one-eye/logging-operator/ avec un Loki pour récupérer les logs de votre container. 
* Déployer votre application avec Helm ou Kustomize.
* Fournir un script (Shell, Terraform, Ansible... ce que vous voulez) pour automatiser la créer d'un Service account avec les droits requis précédemment.

