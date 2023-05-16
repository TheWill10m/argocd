# argocd

This repository contains the configuration I used to deploy applications to my Kubernetes clusters with ArgoCD.

## Install ArgoCD

Firstly, ArgoCD needs to be installed with the below commands.

```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

## Application sets

This repository makes use of ArgoCD ApplicationSets (appsets) to deploy the applications. These can be located in the [appsets](appsets/) directory.

The main appset is the [all](appsets/all/) appset. This is a Kustomize deployment that has `dev` and `prod` overlays. Within each appset there is a `apps` directory, containing a directory of application configuration files for each application to deploy within that appset.

To deploy the `all` appset, use the following command, substituting the environment used.

```
kubectl apply -k appsets/all/overlays/<ENV>/
```
