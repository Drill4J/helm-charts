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
```
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update

helm install ingress-nginx ingress-nginx/ingress-nginx
```

# Yet another install
## Get public IP address of Ingress Controller
IP=xx.xx.xx.xx
## Change . to - in public ip address. Create new variable $URL
export IP=$(echo $IP | sed 's/\./-/g')

## Install drill admin by helm. You need set your URL (admin is web for swagger)
```
URLADMIN=drill-admin.$IP.my.local-ip.co
helm install -n drill --set persistence.enabled=true,ingress.enabled=true,ingress.hosts[0].host=$URLADMIN,ingress.hosts[0].paths[0].path=/ drill-admin drill4j/admin
```

## Install drill admin-ui by helm. You need set your URL
```
URLADMINUI=drill-admin-ui.$IP.my.local-ip.co
helm install -n drill --set ingress.enabled=true,ingress.hosts[0].host=$URLADMINUI,ingress.hosts[0].paths[0].path=/ drill-admin-ui drill4j/admin-ui
```

## Install js-agent by helm. You need set your URL
```
URLJSAGENT=js-agent.$IP.my.local-ip.co
helm install -n drill --set ingress.enabled=true,ingress.hosts[0].host=$URLJSAGENT,ingress.hosts[0].paths[0].path=/ js-agent drill4j/js-agent
```

## Install example-app by helm. You need set your URL
```
URLEXAMPLEAPP=example-app.$IP.my.local-ip.co
helm install -n drill --set ingress.enabled=true,ingress.hosts[0].host=$URLEXAMPLEAPP,ingress.hosts[0].paths[0].path=/ exampleapp drill4j/example-app
```

## Install browser-proxy by helm. You need set your URL
```
URLBROWSERPROXY=browser-proxy.$IP.my.local-ip.co
helm install -n drill --set persistence.enabled=true,ingress.enabled=true,ingress.hosts[0].host=$URLBROWSERPROXY,ingress.hosts[0].paths[0].path=/ browser-proxy drill4j/browser-proxy
```

## Install autotest-extension-dispatcher by helm. You need set your URL
```
helm install -n drill autotest-extension-dispatcher drill4j/autotest-extension-dispatcher
```

For local development of helm-chart please read [Local development](local-development.md)
