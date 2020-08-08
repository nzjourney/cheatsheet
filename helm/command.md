## Helm Basic Command

- How to add new repository:
```sh
$ helm repo add repo-name repo-url
```

- How to search package in repository
```sh
$ helm search repo repo-name
```

- How to update repository:
```sh
$ helm repo update
```

- How to install package:
```sh
$ helm install repo-name/package-name --generate-name
$ helm install released-name repo-name/package-name --namespace namespace-name
```

- How to see what has released using Helm:
```sh
$ helm ls
```

- How to uninstall package:
```sh
$ helm uninstall released-name
```

- How to open Helm help:
```sh
$ helm get -h
```

Refference: https://helm.sh/docs/intro/quickstart/