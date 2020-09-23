1. Make sure you have installed Elasticsearch and Kibana outside kubernetes cluster

2. Create namespace for logging system
```
$ kubectl create ns kube-logging
```

3. Create service account for fluentd
```
$ kubectl create serviceaccount fluentd -n kube-logging
```

4. Create clusterrole for fluentd
```
$ echo "
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: fluentd
  labels:
    app: fluentd
rules:
- apiGroups:
  - ""
  resources:
  - pods
  - namespaces
  verbs:
  - get
  - list
  - watch
" | kubectl apply -f -
```

5. Install dummy random-generator backend server for testing purpose
```
$ echo "
kind: Namespace
apiVersion: v1
metadata:
  name: random-generator
---
# The Deployment which will run our log generator
apiVersion: apps/v1
kind: Deployment
metadata:
  name: random-generator
  namespace: random-generator
  labels:
    app: random-generator
spec:
  selector:
    matchLabels:
      app: random-generator
  template:
    metadata:
      labels:
        app: random-generator
    spec:
      containers:
      - name: random-generator
        imagePullPolicy: Always
        # You can build the image off the source code and push to your own docker hub if you prefer.
        image: chriscmsoft/random-generator:latest
" | kubectl apply -f -
```

6. Install fluentd on daemonset and change value elasticsearch-logging to elasticsearch host
```
$ echo "
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd
  namespace: kube-logging
  labels:
    k8s-app: fluentd-logging
    version: v1
spec:
  selector:
    matchLabels:
      k8s-app: fluentd-logging
      version: v1
  template:
    metadata:
      labels:
        k8s-app: fluentd-logging
        version: v1
    spec:
      serviceAccount: fluentd
      serviceAccountName: fluentd
      tolerations:
      - key: node-role.kubernetes.io/master
        effect: NoSchedule
      containers:
      - name: fluentd
        image: fluent/fluentd-kubernetes-daemonset:v1-debian-elasticsearch
        env:
          - name:  FLUENT_ELASTICSEARCH_HOST
            value: "elasticsearch-logging"
          - name:  FLUENT_ELASTICSEARCH_PORT
            value: "9200"
          - name: FLUENT_ELASTICSEARCH_SCHEME
            value: "http"
          # Option to configure elasticsearch plugin with self signed certs
          # ================================================================
          - name: FLUENT_ELASTICSEARCH_SSL_VERIFY
            value: "true"
          # Option to configure elasticsearch plugin with tls
          # ================================================================
          - name: FLUENT_ELASTICSEARCH_SSL_VERSION
            value: "TLSv1_2"
          # X-Pack Authentication
          # =====================
          #- name: FLUENT_ELASTICSEARCH_USER
          #  value: "elastic"
          #- name: FLUENT_ELASTICSEARCH_PASSWORD
          #  value: "changeme"
          # Logz.io Authentication
          # ======================
          # - name: LOGZIO_TOKEN
          #   value: "ThisIsASuperLongToken"
          # - name: LOGZIO_LOGTYPE
          #   value: "kubernetes"
        resources:
          limits:
            memory: 200Mi
          requests:
            cpu: 100m
            memory: 200Mi
        volumeMounts:
        - name: varlog
          mountPath: /var/log
        - name: varlibdockercontainers
          mountPath: /var/lib/docker/containers
          readOnly: true
      terminationGracePeriodSeconds: 30
      volumes:
      - name: varlog
        hostPath:
          path: /var/log
      - name: varlibdockercontainers
        hostPath:
          path: /var/lib/docker/containers
" | kubectl apply -f -
```

7. Wait until daemonset deployed
```
$ kubectl rollout status daemonset/fluentd -n kube-logging
```

8. Open kibana and set the index pattern

References:
https://docs.fluentd.org/container-deployment/kubernetes
https://github.com/fluent/fluentd-kubernetes-daemonset