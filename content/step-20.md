+++
title = "Observability: Logging"
weight = 20
+++

# Logging

Part of the 12 factor app is to send logs to stdout.
You can gather logs from containers with the `kubectl -n <namespace> logs <pod name> -c <container name if multiple containers in pod>` command.

An application may use multiple containers and it'll be difficult to obtain logs through single containers.
A tool to collect logs of an application will simplify log collection.

Fluentd is an open source log aggregator, collector, processor written in Ruby and is a CNCF graduated project.

Fluent Bit is an open source log aggregator, collector, processor, forwarder written in C. Fluent Bit is a CNCF graduated sub-project under the umbrella of Fluentd.

Both Fluentd and Fluent Bit use plugins to integrate with data sources and data outputs.

Fluent Bit excels in highly distributed environment and has a smaller footprint than FluentD ~450kb vs ~40MB of memory. Both were developed by Treasure Data.

Fluent Bit is not as flexible as Fluentd. Fluent Bit has about 45 plugins versus Fluentd has about 700 plugins.

Fluent Bit has native support for environmental metric collection.

The use of both Fluent Bit and Fluentd optimizes resource consumption on the nodes with Fluent Bit's smaller footprint and optimizes the flexibilty of Fluentd with more than 700 plugins available.

For this tutorial, we'll use Fluentd to collect application logs.

1. Add the fluent helm repo

```ctr:kubernetes
helm repo add fluent https://fluent.github.io/helm-charts
helm repo update
```

Expected output:
```shell
"fluent" has been added to your repositories
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "fluent" chart repository
...Successfully got an update from the "prometheus-community" chart repository
Update Complete. ⎈Happy Helming!⎈
```

2. Install fluentd with the release name of fluentd in the fluentd namespace

```ctr:kubernetes
helm install fluentd fluent/fluentd --namespace kube-system --set variant=elasticsearch8
```

Expected output
```shell
NAME: fluentd
LAST DEPLOYED: Thu Nov  2 07:01:33 2023
NAMESPACE: kube-system
STATUS: deployed
REVISION: 1
NOTES:
Get Fluentd build information by running these commands:

export POD_NAME=$(kubectl get pods --namespace kube-system -l "app.kubernetes.io/name=fluentd,app.kubernetes.io/instance=fluentd" -o jsonpath="{.items[0].metadata.name}")
kubectl --namespace kube-system port-forward $POD_NAME 24231:24231
curl http://127.0.0.1:24231/metrics
```

fluentd is deployed as a DaemonSet resource. A DaemonSet ensures a Pod of the DaemonSet runs on all nodes of the cluster.

Check the fleuntd DaemonSet is running
```ctr:kubernetes
kubectl get daemonset -n fluentd
```

Expected output:
```shell
NAME      DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
fluentd   1         1         0       1            0           <none>          3m25s
```

By default the DaemonSet is configured with Elasticsearch as the fluentd output.
You can verify with the container image 
```ctr:kubernetes
kubectl get ds fluentd -n fluentd -o yaml | grep image:
```

Expected output:
```shell
    image: fluent/fluentd-kubernetes-daemonset:v1.16.2-debian-elasticsearch7-1.0
```


## Install Elasticsearch, Logstash, and Kibana 

The default output for the fluentd helm chart is elasticsearch.
We will install the following:
  - Elasticsearch which is a search/analytics engine and a log analytics tool
  - Logstash to collect logs and send to Elasticsearch for storage and analysis
  - Filebeat to 
  - Kibana to visualize the data stored in Elasticsearch

```ctr:kubernetes
helm repo add elastic https://helm.elastic.co
helm repo update
```

Expected output:
```shell
"elastic" has been added to your repositories
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "fluent" chart repository
...Successfully got an update from the "elastic" chart repository
...Successfully got an update from the "prometheus-community" chart repository
Update Complete. ⎈Happy Helming!⎈
```

## Install Elasticsearch, Logstash, and Kibana 

By default, the fluentd helm chart 

Install Elasticsearch
```ctr:kubernetes
helm install elasticsearch elastic/elasticsearch --set replicas=1 --set minimumMasterNodes=1 --set secret.password=changeme --set resources.requests.cpu=100m --set resources.requests.memory=100Mi --set persistence.enabled=false -n elk --create-namespace
```

Expected output:
```shell
NAME: elasticsearch
LAST DEPLOYED: Thu Nov  2 04:28:58 2023
NAMESPACE: elk
STATUS: deployed
REVISION: 1
NOTES:
1. Watch all cluster members come up.
  $ kubectl get pods --namespace=elk -l app=elasticsearch-master -w
2. Retrieve elastic user's password.
  $ kubectl get secrets --namespace=elk elasticsearch-master-credentials -ojsonpath='{.data.password}' | base64 -d
3. Test cluster health using Helm test.
  $ helm --namespace=elk test elasticsearch
```

Elasticsearch will take a few minutes to be at a `ready` state/
You can check the status of the Elasticsearch pod with `kubectl get pods`
```ctr:kubernetes
kubectl get pods -n elk
```

Expected output:
```shell
NAME                     READY   STATUS    RESTARTS   AGE
elasticsearch-master-0   1/1     Running   0          2m19s
```

Wait until the `elasticsearch-master-0` is ready before proceeding.

Install Logstash
```ctr:kubernetes
helm install logstash elastic/logstash -n elk
```

Expected output:
```shell
NAME: logstash
LAST DEPLOYED: Thu Nov  2 05:42:26 2023
NAMESPACE: elk
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
1. Watch all cluster members come up.
  $ kubectl get pods --namespace=elk -l app=logstash-logstash -w
```

Wait until the logstash pod is ready before proceeding
```ctr:kubernetes
kubectl get pods --namespace=elk -l app=logstash-logstash
```

Expected output:
```shell
NAME                  READY   STATUS    RESTARTS   AGE
logstash-logstash-0   1/1     Running   0          94s
```

Install Filebeat


Install Kibana
```ctr:kubernetes
helm install kibana elastic/kibana --set resources.requests.cpu=100m --set resources.requests.memory=100Mi --set service.type=NodePort --set service.nodePort=30001 -n elk
```

Expected output:
```shell
NAME: kibana
LAST DEPLOYED: Thu Nov  2 05:48:14 2023
NAMESPACE: elk
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
1. Watch all containers come up.
  $ kubectl get pods --namespace=elk -l release=kibana -w
2. Retrieve the elastic user's password.
  $ kubectl get secrets --namespace=elk elasticsearch-master-credentials -ojsonpath='{.data.password}' | base64 -d
3. Retrieve the kibana service account token.
  $ kubectl get secrets --namespace=elk kibana-kibana-es-token -ojsonpath='{.data.token}' | base64 -d
```

Wait until the kibana Pod is ready before proceeding
```ctr:kubernetes
kubectl get pods -n elk -l app=kibana
```

Expected output:
```shell
NAME                             READY   STATUS    RESTARTS   AGE
kibana-kibana-6888db469c-djkv7   1/1     Running   0          111s
```

Use your browser to access kibana
<a href="https://kubernetes.${vminfo:kubernetes:public_ip}.sslip.io:30001" target="_blank">https://kubernetes.${vminfo:kubernetes:public_ip}.sslip.io:30001</a>

Login in with the username = `elastic`, password = `changeme`


---
fluent operator

```ctr:kubernetes
helm install fluent-operator fluent/fluent-operator --create-namespace -n fluent --set containerRuntime=containerd --set es.enable=true --set es.host=http://elasticsearch-master.elk.svc.cluster.local:9200
```

Expected output:
```shell
NAME: fluent-operator
LAST DEPLOYED: Thu Nov  2 07:18:26 2023
NAMESPACE: fluent
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
Thank you for installing  fluent-operator
Your release is named    fluent-operator

To learn more about the release ,try:
   $ helm status  fluent-operator  -n  fluent
   $ helm get  fluent-operator  -n fluent
```

```ctr:kubernetes
cat <<EOF > ~/manifests/fluentd.yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd
  namespace: kube-system
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
      tolerations:
      - key: node-role.kubernetes.io/control-plane
        effect: NoSchedule
      - key: node-role.kubernetes.io/master
        effect: NoSchedule
      containers:
      - name: fluentd
        image: fluent/fluentd-kubernetes-daemonset:v1-debian-elasticsearch
        env:
          - name: K8S_NODE_NAME
            valueFrom:
              fieldRef:
                fieldPath: spec.nodeName
          - name:  FLUENT_ELASTICSEARCH_HOST
            value: "elasticsearch-master.elk.svc.cluster.local"
          - name:  FLUENT_ELASTICSEARCH_PORT
            value: "9200"
          - name: FLUENT_ELASTICSEARCH_SCHEME
            value: "http"
          - name: FLUENT_ELASTICSEARCH_SSL_VERIFY
            value: "true"
          - name: FLUENT_ELASTICSEARCH_SSL_VERSION
            value: "TLSv1_2"
          - name: FLUENT_ELASTICSEARCH_USER
            value: "elastic"
          - name: FLUENT_ELASTICSEARCH_PASSWORD
            value: "changeme"
          - name: LOGZIO_TOKEN
            value: "ThisIsASuperLongToken"
          - name: LOGZIO_LOGTYPE
            value: "kubernetes"
        resources:
          limits:
            memory: 200Mi
          requests:
            cpu: 100m
            memory: 100Mi
        volumeMounts:
        - name: varlog
          mountPath: /var/log
        - name: dockercontainerlogdirectory
          mountPath: /var/log/pods
          readOnly: true
      terminationGracePeriodSeconds: 30
      volumes:
      - name: varlog
        hostPath:
          path: /var/log
      - name: dockercontainerlogdirectory
        hostPath:
          path: /var/log/pods
EOF
```
