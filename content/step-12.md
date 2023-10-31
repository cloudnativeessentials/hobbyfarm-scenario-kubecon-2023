+++
title = "Kubernetes Installation: crictl"
weight = 12
+++

# Kubernetes Prerequisites: crictl

`crictl` is a CLI for CRI-compatible container runtimes. `crictl` is required for kubeadm and the kubelet container runtime interface

1. Install `crictl`:

```ctr:kubernetes
DOWNLOAD_DIR="/usr/local/bin"
sudo mkdir -p "$DOWNLOAD_DIR"
CRICTL_VERSION="v1.28.0"
ARCH="amd64"
curl -L "https://github.com/kubernetes-sigs/cri-tools/releases/download/${CRICTL_VERSION}/crictl-${CRICTL_VERSION}-linux-${ARCH}.tar.gz" | sudo tar -C $DOWNLOAD_DIR -xz
```

Expected output:
```shell
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
100 22.7M  100 22.7M    0     0  12.9M      0  0:00:01  0:00:01 --:--:-- 40.4M
```

Test crictl.
```ctr:kubernetes
crictl --version
```

Expected output:
```shell
crictl version v1.28.0
```

