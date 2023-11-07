+++
title = "Kubernetes Installation: kubectl"
weight = 11
+++

# Install kubectl

kubectl is the Kubernetes cli tool and allows you to run commands against Kubernetes clusters.

1. Download the kubectl binary for the latest release

```ctr:kubernetes
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```

2. Download the kubectl checksum file

```ctr:kubernetes
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"
```

3. Validate the kubectl binary with the checksum file

```ctr:kubernetes
echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check
```

Expected output:

```shell
kubectl: OK
```

4. Install kubectl

```ctr:kubernetes
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

5. Test kubectl

```ctr:kubernetes
kubectl version --client
```

Expected output:

```shell
Client Version: v1.28.2
Kustomize Version: v5.0.4-0.20230601165947-6ce0bf390ce3
```

6. Test kubectl by retrieving the cluster state (we expect this to fail)

```ctr:kubernetes
kubectl cluster-info
```

Expected output:

```shell
E1017 21:31:38.476094    1921 memcache.go:265] couldn't get current server API group list: Get "http://localhost:8080/api?timeout=32s": dial tcp 127.0.0.1:8080: connect: connection refused
E1017 21:31:38.476522    1921 memcache.go:265] couldn't get current server API group list: Get "http://localhost:8080/api?timeout=32s": dial tcp 127.0.0.1:8080: connect: connection refused
E1017 21:31:38.477836    1921 memcache.go:265] couldn't get current server API group list: Get "http://localhost:8080/api?timeout=32s": dial tcp 127.0.0.1:8080: connect: connection refused
E1017 21:31:38.479106    1921 memcache.go:265] couldn't get current server API group list: Get "http://localhost:8080/api?timeout=32s": dial tcp 127.0.0.1:8080: connect: connection refused
E1017 21:31:38.480359    1921 memcache.go:265] couldn't get current server API group list: Get "http://localhost:8080/api?timeout=32s": dial tcp 127.0.0.1:8080: connect: connection refused

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
The connection to the server localhost:8080 was refused - did you specify the right host or port?
```

(Again - this is expexted to fail)