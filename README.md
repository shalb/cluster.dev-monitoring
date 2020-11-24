# Info

Monitoring stack based on [chart](https://github.com/helm/charts/tree/master/stable/prometheus-operator)

# Example app

kubernetes/apps/monitoring/monitoring.yaml

```
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: monitoring
  namespace: argocd
spec:
  destination:
    namespace: monitoring
    server: 'https://kubernetes.default.svc'
  source:
    path: ./
    repoURL: 'https://github.com/shalb/cluster.dev-monitoring'
    targetRevision: update_to_9.3.2
    helm:
      values: |
        prometheus:
          prometheusSpec:
            externalLabels:
              cluster_name: cluster-dev-test
              server_env: test
              project: test
            externalUrl: "https://prometheus.local.lan"
  project: default
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

kubernetes/apps/monitoring/namespace.yaml

```
apiVersion: v1
kind: Namespace
metadata:
  name: monitoring
  labels:
    app: prometheus-operator
```


# Access

## Grafana

Create a proxy connection to your machine

Default credentials:  
User: `admin`  
Password: `prom-operator`


```
export POD_NAME=$(kubectl get pods -n monitoring -l "app=prometheus" -o jsonpath="{.items[0].metadata.name}")
kubectl -n monitoring port-forward --address 127.0.0.1 $POD_NAME 19090:9090
```

Access it via http://127.0.0.1:13000/

# Prometheus

Create a proxy connection to your machine

```
POD_NAME=$(kubectl get pods -n monitoring -l "app.kubernetes.io/name=grafana" -o jsonpath="{.items[0].metadata.name}")
kubectl -n monitoring port-forward --address 127.0.0.1 $POD_NAME 13000:3000
```

Access it via http://127.0.0.1:19090/

