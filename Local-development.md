# Get source
```
git clone https://github.com/Drill4J/helm-charts.git
cd helm-charts
```

## create namespace
```
kubectl create namespace drill
```

## Get ip address on ethenet card
```
export IP=xx.xx.xx.xx
```

## Install drill admin by helm. You need set your URL (admin is web for swagger)
```
URL=drill-admin.$IP.sslip.io
helm install -n drill --set persistence.enabled=true,ingress.enabled=true,ingress.hosts[0].host=$URL,ingress.hosts[0].paths[0].path=/ drill-admin ./admin
```

## Install drill admin-ui by helm. You need set your URL
```
URL=drill-admin-ui.$IP.sslip.io
helm install -n drill --set ingress.enabled=true,ingress.hosts[0].host=$URL,ingress.hosts[0].paths[0].path=/ drill-admin-ui ./admin-ui
```

## Install js-agent by helm. You need set your URL
```
URL=js-agent.$IP.sslip.io
helm install -n drill --set ingress.enabled=true,ingress.hosts[0].host=$URL,ingress.hosts[0].paths[0].path=/ js-agent ./js-agent
```

## Install browser-proxy by helm. You need set your URL
```
helm install -n drill browser-proxy ./browser-proxy
```

## Install autotest-extension-dispatcher by helm. You need set your URL
```
helm install -n drill autotest-extension-dispatcher ./autotest-extension-dispatcher
```


## Install example-app by helm. You need set your URL
```
URL=example-app.$IP.sslip.io
helm install -n drill --set ingress.enabled=true,ingress.hosts[0].host=$URL,ingress.hosts[0].paths[0].path=/ example-app ./example-app
```

# Package helm chart. Create index.yaml for helm chart.
```
helm package admin
helm package admin-ui
helm package example-app
cd ..
helm repo index helm-charts/ --url https://raw.githubusercontent.com/drill4j/helm-charts/main
``` 

# Push new file to Github
```
cd helm-charts
git push
```

# Kubernetes Dashboard
Open https://kubernetes-dashboard.10.66.218.100.sslip.io/

Get token by command
```
kubectl -n kube-system describe secret $(
  kubectl -n kube-system get secret | \
  awk '/^deployment-controller-token-/{print $1}'
) | \
awk '$1=="token:"{print $2}'
```
