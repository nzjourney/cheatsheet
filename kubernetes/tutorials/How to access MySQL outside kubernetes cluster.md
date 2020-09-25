1. Make sure you have installed MySQL database outside kubernetes cluster

2. Create kubernetes service for MySQL, change ip value below to ip and port MySQL
```
$ echo "
kind: "Service"
apiVersion: "v1"
metadata:
  name: "mysql"
spec:
  ports:
    -
      name: "mysql"
      protocol: "TCP"
      port: 3306
      targetPort: 3306
      nodePort: 0
---
kind: "Endpoints"
apiVersion: "v1"
metadata:
  name: "mysql"
subsets:
  -
    addresses:
      -
        ip: "mysql"
    ports:
      -
        port: 3306
        name: "mysql"
" | kubectl apply -f -
```