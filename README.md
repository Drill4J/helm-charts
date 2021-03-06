[![License](https://img.shields.io/github/license/Drill4J/helm-charts)](LICENSE)
[![Visit the website at drill4j.github.io](https://img.shields.io/badge/visit-website-green.svg?logo=firefox)](https://drill4j.github.io/)
[![Telegram Chat](https://img.shields.io/badge/Chat%20on-Telegram-brightgreen.svg)](https://t.me/drill4j)  
![GitHub contributors](https://img.shields.io/github/contributors/Drill4J/helm-charts)
![Lines of code](https://img.shields.io/tokei/lines/github/Drill4J/helm-charts)
![YouTube Channel Views](https://img.shields.io/youtube/channel/views/UCJtegUnUHr0bO6icF1CYjKw?style=social)


- [helm charts for install drill-admin, drill-admin-ui, example-app](#helm-charts-for-install-drill-admin--drill-admin-ui--example-app)
  * [Requirements:](#requirements-)
  * [create namespace](#create-namespace)
  * [Add helm repo drill4j](#add-helm-repo-drill4j)
- [Installation NGINX Ingress Controller](#installation-nginx-ingress-controller)
- [Yet another install](#yet-another-install)
  * [Get External IP Kubernetes NGINX Ingress Controller service](#get-external-ip-kubernetes-nginx-ingress-controller-service)
  * [Check external IP](#check-external-ip)
- [Install Drill](#install-drill)
  * [Install drill admin by helm. You need set your URL (admin is web for swagger)](#install-drill-admin-by-helm-you-need-set-your-url--admin-is-web-for-swagger-)
  * [Install drill admin-ui by helm. You need set your URL](#install-drill-admin-ui-by-helm-you-need-set-your-url)
  * [Install js-agent by helm. You need set your URL](#install-js-agent-by-helm-you-need-set-your-url)
  * [Install example-app by helm. You need set your URL](#install-example-app-by-helm-you-need-set-your-url)
  * [Install browser-proxy by helm. You need set your URL](#install-browser-proxy-by-helm-you-need-set-your-url)
  * [Install autotest-extension-dispatcher by helm. You need set your URL](#install-autotest-extension-dispatcher-by-helm-you-need-set-your-url)


# helm charts for install drill-admin, drill-admin-ui, example-app

## Requirements:
- kubectl
- helm
- exist .kube/config - config for kubernetes

## create namespace
```
kubectl create namespace drill
```

## Add helm repo drill4j
```
helm repo add drill4j https://raw.githubusercontent.com/drill4j/helm-charts/main
helm repo update
```

Install in Kubernetes on Cloud

# Installation NGINX Ingress Controller
When use kubernetes 1.18 then use ingress-nginx version 3.36.0

When use kubernetes 1.22 then use ingress-nginx version latest
```
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx && helm repo update
helm install --atomic nginx-ingress ingress-nginx/ingress-nginx --version 3.36.0
```

# Yet another install
## Get External IP Kubernetes NGINX Ingress Controller service
```
export IP=$(kubectl get services nginx-ingress-ingress-nginx-controller --output jsonpath='{.status.loadBalancer.ingress[0].ip}')
```
## Check external IP
```
echo $IP
```

# Install Drill

## Install drill admin by helm. You need set your URL (admin is web for swagger)
```
URLADMIN=drill-admin.$IP.sslip.io
helm install -n drill --set persistence.enabled=true,ingress.enabled=true,ingress.hosts[0].host=$URLADMIN,ingress.hosts[0].paths[0].path=/ drill-admin drill4j/admin
```

## Install drill admin-ui by helm. You need set your URL
```
URLADMINUI=drill-admin-ui.$IP.sslip.io
helm install -n drill --set ingress.enabled=true,ingress.hosts[0].host=$URLADMINUI,ingress.hosts[0].paths[0].path=/ drill-admin-ui drill4j/admin-ui
```

## Install js-agent by helm. You need set your URL
```
URLJSAGENT=js-agent.$IP.sslip.io
helm install -n drill --set ingress.enabled=true,ingress.hosts[0].host=$URLJSAGENT,ingress.hosts[0].paths[0].path=/ js-agent drill4j/js-agent
```

## Install example-app by helm. You need set your URL
```
URLEXAMPLEAPP=example-app.$IP.sslip.io
helm install -n drill --set ingress.enabled=true,ingress.hosts[0].host=$URLEXAMPLEAPP,ingress.hosts[0].paths[0].path=/ exampleapp drill4j/example-app
```

## Install browser-proxy by helm. You need set your URL
```
URLBROWSERPROXY=browser-proxy.$IP.sslip.io
helm install -n drill --set persistence.enabled=true,ingress.enabled=true,ingress.hosts[0].host=$URLBROWSERPROXY,ingress.hosts[0].paths[0].path=/ browser-proxy drill4j/browser-proxy
```

## Install autotest-extension-dispatcher by helm. You need set your URL
```
helm install -n drill autotest-extension-dispatcher drill4j/autotest-extension-dispatcher
```

# Install Drill to Minikube on Windows
## Requirements: 
- [Install kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/)
- [Install helm](https://helm.sh/docs/intro/install/#from-chocolatey-windows)
- [Install Minikube](https://minikube.sigs.k8s.io/docs/start/)

## Get External IP Minikube
```
$IP=minikube ip
```
## Check external IP
```
echo $IP
```

## Install drill admin by helm. You need set your URL (admin is web for swagger)
```
$URLADMIN='drill-admin.' + $IP + '.sslip.io'
helm install -n drill --set persistence.enabled=true,ingress.enabled=true,ingress.hosts[0].host=$URLADMIN,ingress.hosts[0].paths[0].path=/ drill-admin drill4j/admin
```

## Install drill admin-ui by helm. You need set your URL
```
$URLADMINUI='drill-admin-ui.' + $IP + '.sslip.io'
helm install -n drill --set ingress.enabled=true,ingress.hosts[0].host=$URLADMINUI,ingress.hosts[0].paths[0].path=/ drill-admin-ui drill4j/admin-ui
```


# Basic Authentication
Create file htpasswd
```
sudo apt install apache2-utils
$ htpasswd -c auth user
New password:
Re-type new password:
Adding password for user user
```

Create secret
```
$ kubectl create secret generic basic-auth --from-file=auth -n drill
secret "basic-auth" created
```

Check secret
```
$ kubectl get secret basic-auth -n drill -o yaml
apiVersion: v1
data:
  auth: XXXXXXXXXXXXXXXXXXXXXXX
kind: Secret
metadata:
  name: basic-auth
  namespace: drill
type: Opaque
```

Install drill-admin-ui
```
export IP=xx.xx.xx.xx
URLADMINUI=drill-admin-ui.$IP.sslip.io
helm install -n drill --set ingress.enabled=true,ingress.hosts[0].host=$URLADMINUI,ingress.hosts[0].paths[0].path=/ drill-admin-ui drill4j/admin-ui --set ingress.annotations."nginx\.ingress\.kubernetes\.io/auth-type"=basic  --set ingress.annotations."nginx\.ingress\.kubernetes\.io/auth-secret"=basic-auth --set ingress.annotations."nginx\.ingress\.kubernetes\.io/auth-realm"="Authentication Required - ok"
```

# Use docker secret (for example install drill-admin)
Create secret docker-registry. For add info https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/
```
kubectl create secret docker-registry regcred  -n drill --docker-server=<your-registry-server> --docker-username=<your-name> --docker-password=<your-pword> --docker-email=<your-email>
```

Install drill-admin with docker secret
```
URLADMIN=drill-admin.$IP.sslip.io
helm install -n drill --set persistence.enabled=true,ingress.enabled=true,ingress.hosts[0].host=$URLADMIN,ingress.hosts[0].paths[0].path=/ drill-admin,imagePullSecrets[0].name=regcred drill4j/admin
```

For local development of helm-chart please read [Local development](local-development.md)
