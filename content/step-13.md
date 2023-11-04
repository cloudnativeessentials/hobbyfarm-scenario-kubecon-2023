+++
title = "Cilium"
weight = 13
+++

Install Cilium

1. Install the latest Cilium CLI

```ctr:kubernetes
CILIUM_CLI_VERSION=$(curl -s https://raw.githubusercontent.com/cilium/cilium-cli/main/stable.txt)
CLI_ARCH=amd64
if [ "$(uname -m)" = "aarch64" ]; then CLI_ARCH=arm64; fi
curl -L --fail --remote-name-all https://github.com/cilium/cilium-cli/releases/download/${CILIUM_CLI_VERSION}/cilium-linux-${CLI_ARCH}.tar.gz{,.sha256sum}
sha256sum --check cilium-linux-${CLI_ARCH}.tar.gz.sha256sum
sudo tar xzvfC cilium-linux-${CLI_ARCH}.tar.gz /usr/local/bin
rm cilium-linux-${CLI_ARCH}.tar.gz{,.sha256sum}
```

Expected output

```shell
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
100 34.5M  100 34.5M    0     0  28.6M      0  0:00:01  0:00:01 --:--:-- 97.0M
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
100    92  100    92    0     0    305      0 --:--:-- --:--:-- --:--:--   305
cilium-linux-amd64.tar.gz: OK
cilium
```

2. Install Cilium into the Kubernetes cluster pointed to by your current kubectl context:

```ctr:kubernetes
cilium install --version 1.14.2
```

Expected output:

```shell
ℹ️  Using Cilium version 1.14.2
🔮 Auto-detected cluster name: kubernetes
🔮 Auto-detected kube-proxy has been installed
```

3. Validate the Cilium install

```ctr:kubernetes
cilium status --wait
```

Expected output:

```shell
    /¯¯\
 /¯¯\__/¯¯\    Cilium:             OK
 \__/¯¯\__/    Operator:           OK
 /¯¯\__/¯¯\    Envoy DaemonSet:    disabled (using embedded mode)
 \__/¯¯\__/    Hubble Relay:       disabled
    \__/       ClusterMesh:        disabled

Deployment             cilium-operator    Desired: 1, Ready: 1/1, Available: 1/1
DaemonSet              cilium             Desired: 1, Ready: 1/1, Available: 1/1
Containers:            cilium             Running: 1
                       cilium-operator    Running: 1
Cluster Pods:          2/2 managed by Cilium
Helm chart version:    1.14.2
Image versions         cilium             quay.io/cilium/cilium:v1.14.2@sha256:6263f3a3d5d63b267b538298dbeb5ae87da3efacf09a2c620446c873ba807d35: 1
                       cilium-operator    quay.io/cilium/operator-generic:v1.14.2@sha256:52f70250dea22e506959439a7c4ea31b10fe8375db62f5c27ab746e3a2af866d: 1
```

4. Test the cluster's readiness

```ctr:kubernetes
kubectl get nodes
```

```shell
NAME              STATUS   ROLES           AGE   VERSION
${vminfo:kubernetes:hostname}   Ready    control-plane   25m   v1.28.2
```

5. Check the Kubernetes components running as Pods

```ctr:kubernetes
kubectl get pods -n kube-system
```

Expected output:

```shell
NAME                                      READY   STATUS    RESTARTS   AGE
cilium-operator-5d47789fcb-mft24          1/1     Running   0          31m
cilium-pngps                              1/1     Running   0          31m
coredns-5dd5756b68-qq55f                  1/1     Running   0          48m
coredns-5dd5756b68-qsj62                  1/1     Running   0          48m
etcd-ip-172-31-42-53                      1/1     Running   0          48m
kube-apiserver-ip-172-31-42-53            1/1     Running   0          48m
kube-controller-manager-ip-172-31-42-53   1/1     Running   0          48m
kube-proxy-lhz4p                          1/1     Running   0          48m
kube-scheduler-ip-172-31-42-53            1/1     Running   0          48m
```

