1. Create namespace for monitoring:
```
$ kubectl create ns kube-monitoring
```

2. Add prometheus and grafana helm repository:
```
$ helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
$ helm repo add grafana https://grafana.github.io/helm-charts
$ helm repo update
```
* With helm installation the prometheus will automatically setup daemonset.

3. Install prometheus with helm charts:
```
$ helm install prometheus prometheus-community/prometheus -n kube-monitoring
```

4. Check prometheus pods:
```
$ kubectl get pods -n kube-monitoring | grep prometheus
```

5. Create configmap prometheus for datasource
```
$ echo "
apiVersion: v1
kind: ConfigMap
metadata:
   name: prometheus-grafana-datasource
   namespace: kube-monitoring
   labels:
     grafana_datasource: '1'
data:
  datasource.yaml: |-
    apiVersion: 1
    datasources:
      - name: Prometheus
    type: prometheus
    access: proxy
    orgId: 1
    url: http://prometheus-server.kube-monitoring.svc.cluster.local
" | kubectl apply -f -
```

6. Install grafana with helm chart
```
$ helm install grafana grafana/grafana -n kube-monitoring
```

7. Check the Grafana pods must be running
```
$ kubectl get pods -n kube-monitoring | grep grafana
```

8. Get the Grafana admin password:
```
$ kubectl get secret -namespace kube-monitoring grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```

9. Now you can open the Grafana service with nodeport, ingress, or port forwarding. (This tutorial will be used port forwarding):
```
$ export POD_NAME=$(kubectl get pods -n kube-monitoring -l "app=grafana,release=grafana" -o jsonpath="{.items[0].metadata.name}")
$ kubectl --namespace monitoring port-forward $POD_NAME 3000
```

10. Access url http://localhost:3000 with browser

References:
https://blog.container-solutions.com/how-to-monitor-your-kubernetes-cluster-with-prometheus-and-grafana
