+++
title = "Kubernetes Installation: containerd"
weight = 8
+++

# Kubernetes Preqeuisities: containerd

Since Kubernetes orchestrates containers, we need a container runtime.
Kubernetes uses the Container Runtime Interface (CRI) to interface with a container runtime therefore the container runtime needs to be CRI compliant and OCI compliant. There are several to choose from, we will use containerd, a CNCF graduated project.

## Install containerd

1. Download the containerd archive from https://github.com/containerd/containerd/releases, verify its sha256sum, and extract it under `/usr/local`.

```ctr:kubernetes
wget https://github.com/containerd/containerd/releases/download/v1.7.7/containerd-1.7.7-linux-amd64.tar.gz
```

Expected output ends with:

```shell
2023-10-17 20:08:25 (60.0 MB/s) - ‘containerd-1.7.7-linux-amd64.tar.gz’ saved [46887113/46887113]
```
2. Download the sha256sum.

```ctr:kubernetes
wget https://github.com/containerd/containerd/releases/download/v1.7.7/containerd-1.7.7-linux-amd64.tar.gz.sha256sum
```

Expected output ends with:
```shell
2023-10-17 20:08:51 (5.96 MB/s) - ‘containerd-1.7.7-linux-amd64.tar.gz.sha256sum’ saved [102/102]
```

3. Verify the sha256 checksum

```ctr:kubernetes
sha256sum -c containerd-1.7.7-linux-amd64.tar.gz.sha256sum
```

Expected output:

```shell
containerd-1.7.7-linux-amd64.tar.gz: OK
```

4. Extract the containerd archive into `/usr/local`

```ctr:kubernetes
sudo tar Cxzvf /usr/local containerd-1.7.7-linux-amd64.tar.gz
```

Expected output:

```shell
bin/
bin/containerd-shim-runc-v1
bin/containerd-stress
bin/containerd-shim-runc-v2
bin/containerd
bin/containerd-shim
bin/ctr
```

4. Since we will use systemd to containerd, download the `containerd.service` unit file.

```ctr:kubernetes
sudo wget -P /usr/local/lib/systemd/system https://raw.githubusercontent.com/containerd/containerd/main/containerd.service
```

5. Reload systemd manager configuration to reload all unit files.

```ctr:kubernetes
sudo systemctl daemon-reload
```

6. Enable the containerd service.

```ctr:kubernetes
sudo systemctl enable --now containerd
```

7. Install runc, verify its sha256sum, and install it as /usr/local/sbin/run

Download runc

```ctr:kubernetes
wget https://github.com/opencontainers/runc/releases/download/v1.1.9/runc.amd64
```

Expected output ends with:

```shell
2023-10-17 20:15:08 (93.0 MB/s) - ‘runc.amd64’ saved [10684992/10684992]
```

Download the asc file.

```ctr:kubernetes
wget https://github.com/opencontainers/runc/releases/download/v1.1.9/runc.amd64.asc
```

Expected output ends with:

```shell
2023-10-17 20:15:46 (46.0 MB/s) - ‘runc.amd64.asc’ saved [854/854]
```

8. Install runc

```ctr:kubernetes
sudo install -m 755 runc.amd64 /usr/local/sbin/runc
```

9. containerd uses a configuration file called `config.toml` to specify dameon-level options.
The `config.toml` is located `/etc/containerd/config.toml`

Generate the default `config.toml`

```ctr:kubernetes
sudo mkdir -p /etc/containerd
```

```ctr:kubernetes
containerd config default | sudo tee /etc/containerd/config.toml
```

Expected output will contain _all_ the settings for containerd. It is a significant amount of content.

10. Set the `systemd` cgroup driver with runc
Set the `SystemdCgroup` setting to true

```ctr:kubernetes
sudo sed -i 's@SystemdCgroup = false@SystemdCgroup = true@g' /etc/containerd/config.toml
```

11. Restart containerd

```ctr:kubernetes
sudo systemctl restart containerd
```