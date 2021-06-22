# Install in MicroK8S

## Get ip address on ethenet card
```
export IP=xx.xx.xx.xx
```

## Install drill admin by helm. You need set your URL (admin is web for swagger)
```
URL=drill-admin.$IP.sslip.io
helm install -n drill --set persistence.enabled=true,ingress.enabled=true,ingress.hosts[0].host=$URL,ingress.hosts[0].paths[0].path=/ drill-admin drill4j/admin
```

## Install drill admin-ui by helm. You need set your URL
```
URL=drill-admin-ui.$IP.sslip.io
helm install -n drill --set ingress.enabled=true,ingress.hosts[0].host=$URL,ingress.hosts[0].paths[0].path=/ drill-admin-ui drill4j/admin-ui
```

## Install example-app by helm. You need set your URL
```
URL=example-app.$IP.sslip.io
helm install -n drill --set ingress.enabled=true,ingress.hosts[0].host=$URL,ingress.hosts[0].paths[0].path=/ example-app drill4j/example-app
```
