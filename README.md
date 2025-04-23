# Infra related topics
## Minikube
- mkdir Minikube
- cd Minikube/
- curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
- sudo install minikube-linux-amd64 /usr/local/bin/minikube
- minikube start
- minikube addons list

## helm
- curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
- chmod 700 get_helm.sh
- ./get_helm.sh

# argocd
ArgoCD asennetaan helm charteilla. Halutaan ottaa testiin mukaan myös Sealed secrets.

## Installation
- mkdir -p charts/argo-cd

## Argo-CD helm repo
- helm repo add argo-cd https://argoproj.github.io/argo-helm
- helm dep update charts/argo-cd/


# Sealed secrets
## Kubernetes
- helm repo add sealed-secrets https://bitnami-labs.github.io/sealed-secrets
- helm install sealed-secrets -n kube-system --set-string fullnameOverride=sealed-secrets-controller sealed-secrets/sealed-secrets

## Linux
- KUBESEAL_VERSION='' # Set this to, for example, KUBESEAL_VERSION='0.23.0'
- curl -OL "https://github.com/bitnami-labs/sealed-secrets/releases/download/v${KUBESEAL_VERSION:?}/kubeseal-${KUBESEAL_VERSION:?}-linux-amd64.tar.gz"
- tar -xvzf kubeseal-${KUBESEAL_VERSION:?}-linux-amd64.tar.gz kubeseal
- sudo install -m 755 kubeseal /usr/local/bin/kubeseal