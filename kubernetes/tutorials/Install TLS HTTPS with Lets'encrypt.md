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

1. Install nginx ingress
```
$ kubectl create namespace nginx
$ helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
$ helm repo update
$ helm install nginx ingress-nginx/ingress-nginx --namespace nginx
```

4. Setup dummy backend
```
$ kubectl apply -f https://bit.ly/echo-service
```

5. Change DNS A to external ip address

6. Enable ingress controller to a dummy backend
```
$ echo "
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: javapool.site
spec:
  rules:
  - host: javapool.site
    http:
      paths:
      - path: /
        backend:
          serviceName: echo
          servicePort: 80
" | kubectl apply -f -
```

7. Setup SSL - Create cluster issuer
```
$ echo "
---
apiVersion: cert-manager.io/v1alpha2
kind: ClusterIssuer
metadata:
 name: letsencrypt-prod
 namespace: cert-manager
spec:
 acme:
   server: https://acme-v02.api.letsencrypt.org/directory
   email: nizar.lazuardy@gmail.com
   privateKeySecretRef:
     name: letsencrypt-prod
   solvers:
   - http01:
       ingress:
         class: nginx
" | kubectl apply -f -
```

8.  Edit ingress dummy to add TLS
```
$ echo "
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: javapool.site
  annotations:
    kubernetes.io/ingress.class: nginx
    certmanager.k8s.io/cluster-issuer: letsencrypt-prod
spec:
  backend:
    serviceName: echo
    servicePort: 80
  tls:
  - secretName: javapool.site
    hosts:
    - javapool.site
" | kubectl apply -f -
```