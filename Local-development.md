# Get source
```
git clone https://github.com/Drill4J/helm-charts.git
cd helm-charts
```

## create namespace
```
kubectl create namespace drill
```

## Get public IP address of Ingress Controller
IP=xx.xx.xx.xx
## Change . to - in public ip address. Create new variable $URL
export IP=$(echo $IP | sed 's/\./-/g')

## Install drill admin by helm. You need set your URL (admin is web for swagger)
```
URLADMIN=drill-admin.$IP.my.local-ip.co
helm install --atomic -n drill \
--set persistence.enabled=false \
--set ingress.enabled=true \
--set ingress.hosts[0].host=$URLADMIN \
--set ingress.hosts[0].paths[0].path=/ \
--set DRILL_AGENTS_SOCKET_TIMEOUT="140" \
--set DRILL_DEFAULT_PACKAGES="com/epam/ta/reportportal" \
--set JAVA_TOOL_OPTIONS="-Xmx2g" \
--set LOG_LEVEL="DEBUG" \
--set TEST2CODE_PLUGIN_VERSION="0.7.0-140" \
--set image.tag=latest \
drill-admin ./admin
```

## Install drill admin-ui by helm. You need set your URL
```
URLADMINUI=drill-admin-ui.$IP.my.local-ip.co
helm install --atomic -n drill \
--set ingress.enabled=true \
--set ingress.hosts[0].host=$URLADMINUI \
--set ingress.hosts[0].paths[0].path=/ \
--set image.tag=latest \
drill-admin-ui ./admin-ui
```

## Install browser-proxy by helm. You need set your URL
```
URLBROWSERPROXY=browser-proxy.$IP.my.local-ip.co
helm install --atomic -n drill \
--set persistence.enabled=false \
--set ingress.enabled=true \
--set ingress.hosts[0].host=$URLBROWSERPROXY \
--set ingress.hosts[0].paths[0].path=/ \
browser-proxy ./browser-proxy
```

## Install js-agent by helm. You need set your URL
```
URLJSAGENT=js-agent.$IP.my.local-ip.co
helm install --atomic -n drill \
--set ingress.enabled=true \
--set ingress.hosts[0].host=$URLJSAGENT \
--set ingress.hosts[0].paths[0].path=/ \
js-agent ./js-agent
```

## Install example-app by helm. You need set your URL
```
URLEXAMPLEAPP=example-app.$IP.my.local-ip.co
helm install --atomic -n drill \
--set ingress.enabled=true \
--set ingress.hosts[0].host=$URLEXAMPLEAPP \
--set ingress.hosts[0].paths[0].path=/ \
--set replicaCount=1
example-app ./example-app
```

## Install autotest-extension-dispatcher by helm. You need set your URL
```
helm install --atomic -n drill \
autotest-extension-dispatcher ./autotest-extension-dispatcher
```

# Package helm chart. Create index.yaml for helm chart.
```
helm package admin
helm package admin-ui
helm package autotest-extension-dispatcher
helm package browser-proxy
helm package example-app
helm package js-agent
cd ..
helm repo index helm-charts/ --url https://raw.githubusercontent.com/drill4j/helm-charts/main
``` 

# Push new file to Github
```
cd helm-charts
git push
```

# Kubernetes Dashboard
Open https://kubernetes-dashboard.10.66.218.100..my.local-ip.co

Get token by command
```
kubectl -n kube-system describe secret $(
  kubectl -n kube-system get secret | \
  awk '/^deployment-controller-token-/{print $1}'
) | \
awk '$1=="token:"{print $2}'
```
