+++
title = "Helm"
weight = 17
+++

# Helm

Helm is Kubernetes package manager and a CNCF graduated project. Helm helps you manage applications.

Helm charts are the packaging format. A chart are files in a particulary directory structure that describe related Kubernetes resources.
A chart can be used to deploy something simple like a single Pod with a single container or complex like an application composed of an HTTP server, database, caches, etc.

Helm charts are stored in Helm repositories.

You will use Helm to install several CNCF graduated projects later in this scenario.

1. Install Helm
```ctr:kubernetes
curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash
```

Expected output:
```shell
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 11661  100 11661    0     0  87773      0 --:--:-- --:--:-- --:--:-- 88340
Downloading https://get.helm.sh/helm-v3.13.1-linux-amd64.tar.gz
Verifying checksum... Done.
Preparing to install helm into /usr/local/bin
helm installed into /usr/local/bin/helm
```

2. Verify Helm install
```ctr:kubernetes
helm version --client
```

Expected output:
```shell
version.BuildInfo{Version:"v3.13.1", GitCommit:"3547a4b5bf5edb5478ce352e18858d8a552a4110", GitTreeState:"clean", GoVersion:"go1.20.8"}
```


