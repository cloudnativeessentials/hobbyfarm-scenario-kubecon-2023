+++
title = "Observability: Logging"
weight = 17
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

For this tutorial, we'll use Fluentd to collect logs

## Install Elasticsearch, Logstash, and Kibana 

We will first install Elasticsearch, Logstash, and Kibana aka the ELK stack as the logging backend for fluentd to send logs to.

We will install the following:
  - Elasticsearch which is a search/analytics engine and a log analytics tool
  - Logstash to collect logs and send to Elasticsearch for storage and analysis
  - Filebeat to 
  - Kibana to visualize the data stored in Elasticsearch

1. Add the elastic helm repo.

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


2. Install Elasticsearch.

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

3. Install Logstash

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

Wait until the logstash Pod is ready before proceeding.

```ctr:kubernetes
kubectl get pods --namespace=elk -l app=logstash-logstash
```

Expected output:
```shell
NAME                  READY   STATUS    RESTARTS   AGE
logstash-logstash-0   1/1     Running   0          94s
```

4. Install Kibana.

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

Use your browser to access Kibana
<a href="https://kubernetes.${vminfo:kubernetes:public_ip}.sslip.io:30001" target="_blank">https://kubernetes.${vminfo:kubernetes:public_ip}.sslip.io:30001</a>

Login in with the username = `elastic`, password = `changeme`


## fluentd 

5. Create the required ServiceAccount, ClusterRole, ClusterRoleBinding for fluentd.

```ctr:kubernetes
cat <<EOF > ~/manifests/fluentd-rbac.yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: fluentd
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: fluentd
  namespace: kube-system
rules:
- apiGroups: [""]
  resources:
  - pods
  - namespaces
  verbs:
  - get
  - list
  - watch
---
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: fluentd
roleRef:
  kind: ClusterRole
  name: fluentd
  apiGroup: rbac.authorization.k8s.io
subjects:
- kind: ServiceAccount
  name: fluentd
  namespace: kube-system
EOF
```

6. Apply the manifest.

```ctr:kubernetes
kubectl apply -f ~/manifests/fluentd-rbac.yaml
```

Expected output:

```shell
serviceaccount/fluentd created
clusterrole.rbac.authorization.k8s.io/fluentd created
clusterrolebinding.rbac.authorization.k8s.io/fluentd created
```


7. Create the manifest for the fluentd DaemonSet.

```ctr:kubernetes
cat <<EOF > ~/manifests/fluentd-ds.yaml
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
      serviceAccount: fluentd
      serviceAccountName: fluentd
      tolerations:
      - key: node-role.kubernetes.io/master
        effect: NoSchedule
      containers:
      - name: fluentd
        image: fluent/fluentd-kubernetes-daemonset:elasticsearch
        env:
        - name:  FLUENT_ELASTICSEARCH_HOST
          value: "elasticsearch-master.elk.svc.cluster.local"
        - name:  FLUENT_ELASTICSEARCH_PORT
          value: "9200"
        - name: FLUENT_ELASTICSEARCH_SCHEME
          value: "http"
        - name: FLUENT_UID
          value: "0"
        # X-Pack Authentication
        # =====================
        - name: FLUENT_ELASTICSEARCH_USER
          value: "elastic"
        - name: FLUENT_ELASTICSEARCH_PASSWORD
          value: "changeme"
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
EOF
```

8. Apply the manifest.

```ctr:kubernetes
kubectl apply -f ~/manifests/fluentd-ds.yaml
```

Expected output:

```shell
daemonset.apps/fluentd created
```

9. Navigate to the kibana UI > Management > Index Management to see a new Logstash index generated by the fluentd DaemonSet.
