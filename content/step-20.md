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

2. To install fluentd with the release name of fluentd.

```ctr:kubernetes
helm install fluentd fluent/fluentd
```

Expected output
```shell
NAME: fluentd
LAST DEPLOYED: Thu Oct 19 21:27:11 2023
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
Get Fluentd build information by running these commands:

export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=fluentd,app.kubernetes.io/instance=fluentd" -o jsonpath="{.items[0].metadata.name}")
kubectl --namespace default port-forward $POD_NAME 24231:24231
curl http://127.0.0.1:24231/metrics
```

