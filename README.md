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
- Tehdään Chart.yaml
```
apiVersion: v2
name: argo-cd
version: 1.0.0
dependencies:
  - name: argo-cd
    version: 5.46.8
    repository: https://argoproj.github.io/argo-helm
```
- Tehdään values.yaml
```
argo-cd:
  dex:
    enabled: false
  notifications:
    enabled: false
  applicationSet:
    enabled: false
  server:
    extraArgs:
      - --insecure
```
- helm install argo-cd charts/argo-cd/

## Operointi
- kubectl get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
- admin / *********
- 



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

# MetalLB
- kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.13.10/config/manifests/metallb-native.yaml
- ip addr show eth0
Example of MetalLB configuration: (metallb-config.yaml)
```
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: default-address-pool
  namespace: metallb-system
spec:
  addresses:
    - 172.28.110.240-172.28.110.250
---
apiVersion: metallb.io/v1beta1
kind: L2Advertisement
metadata:
  name: l2adv
  namespace: metallb-system
```
- kubectl apply -f metallb-config.yaml
- kubectl edit svc argo-cd-argocd-server -n default -> Vaihdetaan loadbalanceriksi niin saa IP-osoitteen MetalLB:ltä.
- MetalLB ei toimi kunnolla WSL2.


# Powershell reititykset
## Linux
kubectl port-forward svc/argo-cd-argocd-server 8080:443

## Windows
- PS C:\WINDOWS\system32> netsh interface portproxy add v4tov4 listenport=8080 listenaddress=0.0.0.0 connectport=31479 connectaddress=172.24.101.153
- PS C:\WINDOWS\system32> netsh interface portproxy add v4tov4 listenport=8443 listenaddress=0.0.0.0 connectport=31479 connectaddress=172.24.101.153