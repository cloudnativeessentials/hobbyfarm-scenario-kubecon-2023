+++
title = "Observability: Metrics"
weight = 18
+++

There are 3 pillars of observability: metrics, traces, and logs.
In this tutorial, you will install monitoring and logging solutions.
Let's start with monitoring.

Prometheus stores and collects metrics as time series data.
Prometheus is a monitoring and alerting toolkit and is a CNCF graduated project.

There are multiple ways to install Prometheus.
The kube-prometheus-stack is a community-maintained helm chart that is a "batteries-included" chart that includes the following:
- Prometheus Operator
- Prometheus with Prometheus rules
- Alertmanager
- Prometheus node-exporter
- Prometheus Adapter for Kubernetes Metrics APIs
- kube-state-metrics
- Grafana with dashboards

1. Add the Prometheus community helm repository.
```ctr:kubernetes
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
```

Expected output:
```shell
"prometheus-community" has been added to your repositories
```

2. Update information of available charts locally from chart repositories
```ctr:kubernetes
helm repo update
```

Expected output:
```shell
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "prometheus-community" chart repository
Update Complete. ⎈Happy Helming!⎈
```

3. Install
```ctr:kubernetes
helm install kube-prometheus-stack prometheus-community/kube-prometheus-stack \
--set alertmanager.service.type=NodePort \
--set prometheusOperator.service.type=NodePort \
--set prometheus.service.type=NodePort \
--set grafana.service.type=NodePort \
--set grafana.service.nodePort=30140 \
--namespace kube-prometheus-stack \
--create-namespace \
--set namespaceOverride=kube-prometheus-stack \
--set grafana.namespaceOverride=kube-prometheus-stack \
--set kube-state-metrics.namespaceOverride=kube-prometheus-stack \
--set prometheus-node-exporter.namespaceOverride=kube-prometheus-stack
```

Expected output:
```shell
NAME: kube-prometheus-stack
LAST DEPLOYED: Wed Oct 18 21:31:24 2023
NAMESPACE: kube-prometheus-stack
STATUS: deployed
REVISION: 1
NOTES:
kube-prometheus-stack has been installed. Check its status by running:
  kubectl --namespace kube-prometheus-stack get pods -l "release=kube-prometheus-stack"

Visit https://github.com/prometheus-operator/kube-prometheus for instructions on how to create & configure Alertmanager and Prometheus instances using the Operator.
```

4. Check the status of the installation.

```ctr:kubernetes
kubectl --namespace kube-prometheus-stack get pods -l "release=kube-prometheus-stack"
```

Expected output:
```shell
NAME                                                        READY   STATUS    RESTARTS   AGE
kube-prometheus-stack-kube-state-metrics-6bdd78dc74-4frg8   1/1     Running   0          44s
kube-prometheus-stack-operator-67bc68d75-h8z5d              1/1     Running   0          44s
kube-prometheus-stack-prometheus-node-exporter-nhpzn        1/1     Running   0          44s
```

5. Kubernetes Services expose applications. A Service type of NodePort exposes Services at each Kubernetes nodes' IP at a static port from 30000 to 32767. Check the services

```ctr:kubernetes
kubectl get services -n kube-prometheus-stack
```

Expected output:
```shell
NAME                                             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                         AGE
alertmanager-operated                            ClusterIP   None            <none>        9093/TCP,9094/TCP,9094/UDP      13s
kube-prometheus-stack-alertmanager               NodePort    10.102.162.47   <none>        9093:30903/TCP,8080:32403/TCP   18s
kube-prometheus-stack-grafana                    NodePort    10.110.209.69   <none>        80:30140/TCP                    18s
kube-prometheus-stack-kube-state-metrics         ClusterIP   10.107.28.248   <none>        8080/TCP                        18s
kube-prometheus-stack-operator                   NodePort    10.111.61.81    <none>        443:30443/TCP                   18s
kube-prometheus-stack-prometheus                 NodePort    10.96.184.91    <none>        9090:30090/TCP,8080:30167/TCP   18s
kube-prometheus-stack-prometheus-node-exporter   ClusterIP   10.97.91.228    <none>        9100/TCP                        18s
prometheus-operated                              ClusterIP   None            <none>        9090/TCP                        11s
```

6. To access Prometheus go to your node's IP on port 30090, [http://${vminfo:kubernetes:public_ip}.sslip.io:30090](http://${vminfo:kubernetes:public_ip}.sslip.io:30090)

7. To access Grafana go to your node's IP on port 30140, [http://${vminfo:kubernetes:public_ip}.sslip.io:30140](http://${vminfo:kubernetes:public_ip}.sslip.io:30140) and use the default admin credentials: username = `admin`, password = `prom-operator`
Checkout a dashboard like the Kubernetes/Kubelet dashboard



