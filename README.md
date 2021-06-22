# helm charts for install drill-admin, drill-admin-ui, example-app

## Requirements:
- kubectl
- helm
- exist .kube/config - config for kubernetes

## create namespace
```
kubectl create namespace drill
```

## Add helm pepo drill4j
```
helm repo add drill4j https://raw.githubusercontent.com/drill4j/helm-charts/main
helm repo update
```

## Install drill admin by helm. You need set your URL (admin is web for swagger)
```
URL=drill-admin.10.66.218.100.sslip.io
helm install -n drill --set persistence.enabled=true,ingress.enabled=true,ingress.hosts[0].host=$URL,ingress.hosts[0].paths[0].path=/ drill-admin drill4j/admin
```

## Install drill admin-ui by helm. You need set your URL
```
URL=drill-admin-ui.10.66.218.100.sslip.io
helm install -n drill --set ingress.enabled=true,ingress.hosts[0].host=$URL,ingress.hosts[0].paths[0].path=/ drill-admin-ui drill4j/admin-ui
```

## Install example-app by helm. You need set your URL
```
URL=example-app.10.66.218.100.sslip.io
helm install -n drill --set ingress.enabled=true,ingress.hosts[0].host=$URL,ingress.hosts[0].paths[0].path=/ example-app drill4j/example-app
```


# Installation NGINX Ingress Controller
```
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update

helm install ingress-nginx ingress-nginx/ingress-nginx
```

# Yet another install
## Get public IP address of Ingress Controller
PUBLICIP=xx.xx.xx.xx
## Change . to - in public ip address. Create new variable $URL
PUBLICIPDASH=xx-xx-xx-xx

## Create variable for URL of admin
## Install drill admin by helm. You need set your URL (admin is web for swagger)
```
URLADMIN=admin.$PUBLICIPDASH.my.local-ip.co
helm install -n drill --set persistence.enabled=true,ingress.enabled=true,ingress.hosts[0].host=$URLADMIN,ingress.hosts[0].paths[0].path=/ drill-admin drill4j/admin
```

## Create variable for URL of admin-ui
## Install drill admin-ui by helm. You need set your URL
```
URLADMINUI=adminui.$PUBLICIPDASH.my.local-ip.co
helm install -n drill --set ingress.enabled=true,ingress.hosts[0].host=$URLADMINUI,ingress.hosts[0].paths[0].path=/ drill-admin-ui drill4j/admin-ui
```

## Create variable for URL of admin-ui
## Install example-app by helm. You need set your URL
```
URLEXAMPLEAPP=exampleapp.$PUBLICIPDASH.my.local-ip.co
helm install -n drill --set ingress.enabled=true,ingress.hosts[0].host=$URLEXAMPLEAPP,ingress.hosts[0].paths[0].path=/ exampleapp drill4j/example-app
```
