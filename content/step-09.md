+++
title = "Kubernetes Installation: cni and crictl"
weight = 9
+++

## CNI installation

CNI plugins are required to implement the Kubernetes network model.

1. Install CNI plugins.

```ctr:kubernetes
CNI_PLUGINS_VERSION="v1.3.0"
ARCH="amd64"
DEST="/opt/cni/bin"
```

```ctr:kubernetes
sudo mkdir -p "$DEST"
```

```ctr:kubernetes
curl -L "https://github.com/containernetworking/plugins/releases/download/${CNI_PLUGINS_VERSION}/cni-plugins-linux-${ARCH}-${CNI_PLUGINS_VERSION}.tgz" | sudo tar -C "$DEST" -xz
```

Expected output:
```shell
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
100 43.2M  100 43.2M    0     0  25.0M      0  0:00:01  0:00:01 --:--:-- 43.0M
```

## Kubernetes Prerequisites: crictl

`crictl` is a CLI for CRI-compatible container runtimes. `crictl` is required for kubeadm and the kubelet container runtime interface

2. Install `crictl`:

```ctr:kubernetes
DOWNLOAD_DIR="/usr/local/bin"
```

```ctr:kubernetes
sudo mkdir -p "$DOWNLOAD_DIR"
```

```ctr:kubernetes
CRICTL_VERSION="v1.28.0"
```

```ctr:kubernetes
ARCH="amd64"
```

```ctr:kubernetes
curl -L "https://github.com/kubernetes-sigs/cri-tools/releases/download/${CRICTL_VERSION}/crictl-${CRICTL_VERSION}-linux-${ARCH}.tar.gz" | sudo tar -C $DOWNLOAD_DIR -xz
```

Expected output:

```shell

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
100 22.7M  100 22.7M    0     0  12.9M      0  0:00:01  0:00:01 --:--:-- 40.4M
```

2. Test crictl

```ctr:kubernetes
crictl --version
```

Expected output:

```shell
crictl version v1.28.0
```

