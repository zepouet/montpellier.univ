# PROPOSITION 1

* Créer deux utilisateurs <prefixe>-dev et <prefixe>-ops
* Créer deux dépots DEV / OPS dans Gitlab
  * DEV : uniquement le code source PHP, Node (droi
  * OPS : 
    * .gitlab-ci.yaml
    * Dockerfile 
    * Service Account en Variable masquée limité au namespace avec accès pour POD/DEPLOYMENT/CONFIGMAP uniquement
  
# PROPOSTION 2

Au choix:
* Mettre en place un Vault (mozilla SOPS ou Hashicorp)
* Utiliser un Service Mesh Istio ou Linkerd
