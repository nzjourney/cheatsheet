1. Install helm (https://helm.sh/)
```
$ curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3
$ chmod 700 get_helm.sh
$ ./get_helm.sh
```

2. Install cert-manager
with regular manifests (kubernetes < v1.16)
```
$ kubectl apply --validate=false -f https://github.com/jetstack/cert-manager/releases/download/v1.0.0/cert-manager-legacy.yaml
```

with regular manifests (kubernetes v1.16+)
```
$ kubectl apply --validate=false -f https://github.com/jetstack/cert-manager/releases/download/v1.0.0/cert-manager.yaml
```

with Helm v3
```
$ kubectl create namespace cert-manager
$ helm repo add jetstack https://charts.jetstack.io
$ helm repo update
$ helm install cert-manager jetstack/cert-manager --namespace cert-manager --version v0.16.1 --set installCRDs=true
```

3. Install nginx ingress
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

4. Setup dummy backend
```
$ kubectl create namespace apps
$ kubectl apply -f https://bit.ly/echo-service -n apps
```

5. Create Secret
```
$ echo "
apiVersion: v1
kind: Secret
metadata:
  name: nizarlazuardy.site
  namespace: apps
data:
  tls.crt: <cat yourcertfile.crt | base64>
  tls.key: <cat yourkeyfile.key | base64>
" | kubectl apply -f -
```

6. Create Issuer
```
$ echo "
apiVersion: cert-manager.io/v1
kind: Issuer
metadata:
  name: nizarlazuardy.site
  namespace: apps
spec:
  ca:
    secretName: nizarlazuardy.site
" | kubectl apply -f -
```

7. Enable ingress controller to a dummy backend
```
$ echo "
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: nizarlazuardy.site
  namespace: apps
  annotations:
    kubernetes.io/ingress.class: "nginx"
    cert-manager.io/issuer: "nizarlazuardy.site"
spec:
  tls:
  - hosts:
    - nizarlazuardy.site
    secretName: ca-key-pair
  rules:
  - host: nizarlazuardy.site
    http:
      paths:
        - path: /
          backend:
            serviceName: echo
            servicePort: 80
" | kubectl apply -f -
```

8. Create A DNS for echo server to external ip address, wait until propagated done (max 24h)

9. Access your http (auto redirect to https) or https address domain with browser
