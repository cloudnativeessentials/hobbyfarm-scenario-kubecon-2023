+++
title = "Kubernetes Installation: cni"
weight = 11
+++


# CNI installation

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


