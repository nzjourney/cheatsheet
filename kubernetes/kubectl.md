## Kubernetes Kubectl Basic Command

- How to see cluster information:
```sh
$ kubectl cluster-info 
```

- How to see API version:
```sh
$ kubectl api-version
```

- How to configure a resource by filename:
```sh
$ kubectl apply -f file.yml
```

- How to create a resource by filename:
```sh
$ kubectl create -f file.yml
```

- How to get all information:
```sh
$ kubectl get all
$ kubectl get all -n namespace-name
```

- How to get pods:
```sh
$ kubectl get po
$ kubectl get pods
$ kubectl get pods -n namespace
```

- How to get nodes:
```sh
$ kubectl get no
$ kubectl get nodes
```

- How to get namespace:
```sh
$ kubectl get ns
$ kubectl get namespace
```

- How to get service:
```sh
$ kubectl get svc
$ kubectl get services
```

- How to get endpoints:
```sh
$ kubectl get ep
$ kubectl get endpoints
```

- How to get resources:
```sh
$ kubectl get rs
```

- How to get custom resources:
```sh
$ kubectl get crd
```

- How to get cluster issuer:
```sh
$ kubectl get clusterissuer
```

- How to get deployment:
```sh
$ kubectl get deployment
```

- How to delete deployment:
```sh
$ kubectl delete deployment deployment-name
```

- How to get daemonset:
```sh
$ kubectl get daemonset
$ kubectl get daemonset -n namespace
```

- How to get describe a pod:
```sh
$ kubectl describe pod pod-name
```

- How to get config cluster:
```sh
$ kubectl config get-cluster
$ kubectl config get-cluster cluster-name
```

- How to get logs from a pod:
```sh
$ kubectl logs pod-name
```

- How to display resources usage of node:
```sh
$ kubectl top node node-name
```

- How to execute a shell command inside pod:
```sh
$ kubectl exec pod-name some-command
```

- How to remote container inside pod:
```sh
$ kubectl exec -it pod-name /bin/sh
```

- How to create port forwarding:
```sh
$ kubectl port-forward pod-name local-port:remote-port
```
