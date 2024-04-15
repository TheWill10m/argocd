# argocd

This repository contains the configuration I used to deploy applications to my Kubernetes clusters with ArgoCD.

## Install ArgoCD

Firstly, ArgoCD needs to be installed with the below commands.

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

## Configuring external access

For external access, I use ingress-nginx as the k8s ingress controller.

Install the ingress-nginx helm chart as a argocd application.

```bash
kubectl apply -f apps/ingress-nginx.yaml
```

For TLS termination, you will need a secret configured with the TLS certificate you wish to use.

```bash
kubectl create secret tls ${SECRET_NAME} --key ${KEY_PATH} --cert ${CERT_PATH}
```

ArgoCD will need to be configure to use HTTP only. This is done with a configmap. Then the ingress can be applied. You may wish to edit the `ingress.yaml` file to use your desired URL.

```bash
kubectl apply -f argocd-cmd-params-cm.yaml
kubectl apply -f ingress.yaml
```

You will then be able to access the ArgoCD web UI from the configured URL.

The initial admin password can be read from the secret `argocd-initial-admin-secret`.

```bash
k get secret argocd-initial-admin-secret --template={{.data.password}} | base64 --decode
```

## Application sets - deprecated

This repository makes use of ArgoCD ApplicationSets (appsets) to deploy the applications. These can be located in the [appsets](appsets/) directory.

The main appset is the [all](appsets/all/) appset. This is a Kustomize deployment that has `dev` and `prod` overlays. Within each appset there is a `apps` directory, containing a directory of application configuration files for each application to deploy within that appset.

To deploy the `all` appset, use the following command, substituting the environment used.

```bash
kubectl apply -k appsets/all/overlays/<ENV>/
```
