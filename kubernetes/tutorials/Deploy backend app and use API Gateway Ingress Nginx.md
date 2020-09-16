1. Install helm (https://helm.sh/)
```
$ curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3
$ chmod 700 get_helm.sh
$ ./get_helm.sh
```

2. Install nginx ingress
with regular manifests
```
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.35.0/deploy/static/provider/cloud/deploy.yaml
```

with Helm v3
```
$ kubectl create namespace nginx
$ helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
$ helm repo update
$ helm install nginx ingress-nginx/ingress-nginx --namespace nginx
```

3. Setup dummy backend
```
$ kubectl create namespace apps
$ kubectl apply -f https://bit.ly/echo-service -n apps
```

4. Enable ingress controller to a dummy backend
```
$ echo "
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: nizarlazuardy.site
  namespace: apps
spec:
  rules:
  - http:
      paths:
        - path: /
          backend:
            serviceName: echo
            servicePort: 80
" | kubectl apply -f -
```

5. Create A DNS for echo server to external ip address, wait until propagated done (max 24h)

6. Access your http address domain with browser