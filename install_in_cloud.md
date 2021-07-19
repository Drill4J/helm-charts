1. Create, configure Kubernetes in the Cloud
2. Install Ingress:
```
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
helm install ingress-nginx ingress-nginx/ingress-nginx
export EXTERNALIP=$(kubectl get services ingress-nginx-controller --output jsonpath='{.status.loadBalancer.ingress[0].ip}')
echo $EXTERNALIP
```

3. Create namespace drill
```
kubectl create namespace drill
```

3. Install drilladmin
```
helm install --atomic -n drill \
--set persistence.enabled=false \
--set ingress.enabled=true \
--set ingress.hosts[0].host=drill-admin.$EXTERNALIP.sslip.io \
--set ingress.hosts[0].paths[0].path=/ \
--set ingress.hosts[0].paths[0].pathType="ImplementationSpecific" \
--set DRILL_AGENTS_SOCKET_TIMEOUT="140" \
--set DRILL_DEFAULT_PACKAGES="com/epam/ta/reportportal" \
--set JAVA_TOOL_OPTIONS="-Xmx2g" \
--set LOG_LEVEL="DEBUG" \
--set TEST2CODE_PLUGIN_VERSION="0.8.0-7" \
drill-admin ./admin
```

4. Install browser-proxy in Cloud
```
helm install --atomic -n drill \
--set persistence.enabled=false \
--set ingress.enabled=true \
--set ingress.hosts[0].host=browser-proxy.$EXTERNALIP.sslip.io \
--set ingress.hosts[0].paths[0].path=/ \
--set ingress.hosts[0].paths[0].pathType="ImplementationSpecific" \
browser-proxy ./browser-proxy
```

5. Install drill-admin-ui in Cloud
```
helm install --atomic -n drill \
--set ingress.enabled=true \
--set ingress.hosts[0].host=drill-admin-ui.$EXTERNALIP.sslip.io \
--set ingress.hosts[0].paths[0].path=/ \
--set ingress.hosts[0].paths[0].pathType="ImplementationSpecific" \
--set image.repository=antonpatsev/admin-ui \
--set image.tag=master \
drill-admin-ui ./admin-ui
```

6. Install js-agent in cloud
```
helm install --atomic -n drill \
--set ingress.enabled=true \
--set ingress.hosts[0].host=js-agent.$EXTERNALIP.sslip.io \
--set ingress.hosts[0].paths[0].path=/ \
--set ingress.hosts[0].paths[0].pathType="ImplementationSpecific" \
js-agent ./js-agent
```

7. Install example-app in cloud
```
helm install --atomic -n drill \
--set ingress.enabled=true \
--set ingress.hosts[0].host=example-app.$EXTERNALIP.sslip.io \
--set ingress.hosts[0].paths[0].path=/ \
--set ingress.hosts[0].paths[0].pathType="ImplementationSpecific" \
--set replicaCount=1 \
example-app ./example-app
```