# Règles

Vous avez les choix entre ces deux propositions de TP.
La seconde est plus compliquée et je serai donc plus tolérant à certaines erreurs pour remercier ce qui se lance dessus.
Néanmoins vous ne serez pas mal noté si vous choisissez la première.
Vous ne partez pas tous avec la même expérience.

Choisissez une application quelqueconque dans votre langage favori qui affiche un "C'était le 26 mai 93" en page web et aussi sur la sortie standard.
L'application sait lire une configMap (même namespace forcèment) pour afficher le nom de buteur. Je vous laisse chercher sur Google.
Pas besoin de CSS. Le but est de respecter les https://12factor.net/fr/

# PROPOSITION 1

Contexte : Un développeur doit fournir une solution cloud native basé sur Docker / K8S. 
Il est seul à développer donc pas de souci niveau droit. 

* Créer un utilisateur <prefixe>-dev (nous prendrons xxx-dev pour exemple)
* Créer un dépots PROJET dans Gitlab
  * PROJET : uniquement le code source PHP, Node... java
    * Droits MAINTENER pour xxx-dev
    * Contient les fichiers suivants:
      * .gitlab-ci.yaml
      * Dockerfile 
      * Service Account en Variable masquée 

## Remarques : 
* Créer un service account limitée au namespace avec accès total pour POD/REPLICASET/DEPLOYMENT/CONFIGMAP uniquement. Pour réaliser ceci vous devvez livrer un fichier explicitant l'ensemble des commandes que vous devez jouer en étant admin du cluster K8S. Forcèment pour avoir le droits de créer des SA, il faut être les droits nécessaires.
* Ne pas utiliser DinD (Docker in Docker) pour builder l'image Docker dans .gitlab-ci. Choisir entre Buildah, Kaniko ou en utilisant la Socket Docker dans un runner même si cette solution la moins sécurisée est très pertinente dans une infra non mutualisée, non exposée à des tiers.
* Bien faire attention à pousser les logs de votre application sur la sortie standard.
 
# PROPOSTION 2

Contexte : similer le fait d'avoir des utilisateurs différentes qui ont des droits différents.
Typiquement une agence web pour laquelle vous allez mettre un dépôt avec les droits DEVELOPPER.
 
* Créer deux utilisateurs <prefixe>-dev et <prefixe>-ops (nous prendrons xxx-dev et xxx-ops pour exemple)
* Créer deux dépots DEV / OPS dans Gitlab
  * DEV : uniquement le code source PHP, Node... java
    * Droits DEVELOPER pour xxx-dev
    * Droits MAINTENER pour xxx-ops
  * OPS 
    * Droits READER pour xxx-dev
    * Droits MAINTENER pour xxx-ops
    Contient les fichiers suivants:
      * .gitlab-ci.yaml
      * Dockerfile 
      * Service Account en Variable masquée limitée au namespace avec accès pour POD/DEPLOYMENT/CONFIGMAP uniquement

## Remarques : 
* Créer un service account limitée au namespace avec accès total pour POD/REPLICASET/DEPLOYMENT/CONFIGMAP uniquement. Pour réaliser ceci vous devvez livrer un fichier explicitant l'ensemble des commandes que vous devez jouer en étant admin du cluster K8S. Forcèment pour avoir le droits de créer des SA, il faut être les droits nécessaires.
* Ne pas utiliser DinD (Docker in Docker) pour builder l'image Docker dans .gitlab-ci. Choisir uniquement entre Buildah et Kaniko. De même il est interdit d'utiliser la Socket Docker car environnement mutualisée possible. Solution pas sécurisée.
* Bien faire attention à pousser les logs de votre application sur la sortie standard. 

## Extra 
 
Tout n'est pas à faire sauf si vous en rêvez.
 
* Mettre en place un Vault pour éviter de stocker le S.A. en tant que variable masquée dans Gitlab. Choix possible entre mozilla SOPS, Hashicorp Vault ou même https://github.com/bitnami-labs/sealed-secrets. 
* Déployer une solution comme https://banzaicloud.com/docs/one-eye/logging-operator/ avec un Loki pour récupérer les logs de votre container. 
* Déployer votre application avec Helm ou Kustomize.
