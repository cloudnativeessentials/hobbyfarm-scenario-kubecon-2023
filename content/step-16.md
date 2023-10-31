+++
title = "Cilium"
weight = 16
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

4. Validate the cluster's network connectivity (test takes several minutes)

```ctr:kubernetes
cilium connectivity test
```

Expected output:
```shell
ℹ️  Single-node environment detected, enabling single-node connectivity test
ℹ️  Monitor aggregation detected, will skip some flow validation steps
✨ [kubernetes] Creating namespace cilium-test for connectivity check...
✨ [kubernetes] Deploying echo-same-node service...
✨ [kubernetes] Deploying DNS test server configmap...
✨ [kubernetes] Deploying same-node deployment...
✨ [kubernetes] Deploying client deployment...
✨ [kubernetes] Deploying client2 deployment...
⌛ [kubernetes] Waiting for deployment cilium-test/client to become ready...
⌛ [kubernetes] Waiting for deployment cilium-test/client2 to become ready...
⌛ [kubernetes] Waiting for deployment cilium-test/echo-same-node to become ready...
⌛ [kubernetes] Waiting for CiliumEndpoint for pod cilium-test/client-78f9dffc84-26xcf to appear...
⌛ [kubernetes] Waiting for CiliumEndpoint for pod cilium-test/client2-59b578d4bb-fxw2k to appear...
⌛ [kubernetes] Waiting for pod cilium-test/client-78f9dffc84-26xcf to reach DNS server on cilium-test/echo-same-node-79d996cb79-6jjrh pod...
⌛ [kubernetes] Waiting for pod cilium-test/client2-59b578d4bb-fxw2k to reach DNS server on cilium-test/echo-same-node-79d996cb79-6jjrh pod...
⌛ [kubernetes] Waiting for pod cilium-test/client-78f9dffc84-26xcf to reach default/kubernetes service...
⌛ [kubernetes] Waiting for pod cilium-test/client2-59b578d4bb-fxw2k to reach default/kubernetes service...
⌛ [kubernetes] Waiting for CiliumEndpoint for pod cilium-test/echo-same-node-79d996cb79-6jjrh to appear...
⌛ [kubernetes] Waiting for Service cilium-test/echo-same-node to become ready...
⌛ [kubernetes] Waiting for Service cilium-test/echo-same-node to be synchronized by Cilium pod kube-system/cilium-pngps
⌛ [kubernetes] Waiting for NodePort 172.31.42.53:31959 (cilium-test/echo-same-node) to become ready...
ℹ️  Skipping IPCache check
🔭 Enabling Hubble telescope...
⚠️  Unable to contact Hubble Relay, disabling Hubble telescope and flow validation: rpc error: code = Unavailable desc = connection error: desc = "transport: Error while dialing: dial tcp 127.0.0.1:4245: connect: connection refused"
ℹ️  Expose Relay locally with:
   cilium hubble enable
   cilium hubble port-forward&
ℹ️  Cilium version: 1.14.2
🏃 Running tests...
[=] Test [no-policies]
..................
[=] Test [no-policies-extra]
..
[=] Test [allow-all-except-world]
........
[=] Test [client-ingress]
..
[=] Test [client-ingress-knp]
..
[=] Test [allow-all-with-metrics-check]
..
[=] Test [all-ingress-deny]
......
[=] Test [all-ingress-deny-knp]
......
[=] Test [all-egress-deny]
........
[=] Test [all-egress-deny-knp]
........
[=] Test [all-entities-deny]
......
[=] Test [cluster-entity]
..
[=] Test [host-entity]
..
[=] Test [echo-ingress]
..
[=] Test [echo-ingress-knp]
..
[=] Test [client-ingress-icmp]
..
[=] Test [client-egress]
..
[=] Test [client-egress-knp]
..
[=] Test [client-egress-expression]
..
[=] Test [client-egress-expression-knp]
..
[=] Test [client-with-service-account-egress-to-echo]
..
[=] Test [client-egress-to-echo-service-account]
..
[=] Test [to-entities-world]
......
[=] Test [to-cidr-external]
....
[=] Test [to-cidr-external-knp]
....
[=] Test [echo-ingress-from-other-client-deny]
....
[=] Test [client-ingress-from-other-client-icmp-deny]
....
[=] Test [client-egress-to-echo-deny]
....
[=] Test [client-ingress-to-echo-named-port-deny]
..
[=] Test [client-egress-to-echo-expression-deny]
..
[=] Test [client-with-service-account-egress-to-echo-deny]
..
[=] Test [client-egress-to-echo-service-account-deny]
.
[=] Test [client-egress-to-cidr-deny]
....
[=] Test [client-egress-to-cidr-deny-default]
....
[=] Test [health]
.
[=] Skipping Test [north-south-loadbalancing] (Feature node-without-cilium is disabled)
[=] Skipping Test [pod-to-pod-encryption] (Feature encryption-pod is disabled)
[=] Skipping Test [node-to-node-encryption] (Feature encryption-pod is disabled)
[=] Skipping Test [egress-gateway-excluded-cidrs] (Feature enable-ipv4-egress-gateway is disabled)
[=] Skipping Test [pod-to-node-cidrpolicy] (Feature cidr-match-nodes is disabled)
[=] Skipping Test [north-south-loadbalancing-with-l7-policy] (Feature node-without-cilium is disabled)
[=] Test [echo-ingress-l7]
......
[=] Test [echo-ingress-l7-named-port]
......
[=] Test [client-egress-l7-method]
......
[=] Test [client-egress-l7]
........
[=] Test [client-egress-l7-named-port]
........
[=] Skipping Test [client-egress-l7-tls-deny-without-headers] (Feature secret-backend-k8s is disabled)
[=] Skipping Test [client-egress-l7-tls-headers] (Feature secret-backend-k8s is disabled)
[=] Skipping Test [client-egress-l7-set-header] (Feature secret-backend-k8s is disabled)
[=] Skipping Test [echo-ingress-auth-always-fail] (Feature mutual-auth-spiffe is disabled)
[=] Skipping Test [echo-ingress-mutual-auth-spiffe] (Feature mutual-auth-spiffe is disabled)
[=] Skipping Test [pod-to-ingress-service] (Feature ingress-controller is disabled)
[=] Skipping Test [pod-to-ingress-service-deny-all] (Feature ingress-controller is disabled)
[=] Skipping Test [pod-to-ingress-service-allow-ingress-identity] (Feature ingress-controller is disabled)
[=] Test [dns-only]
........
[=] Test [to-fqdns]
........

✅ All 42 tests (182 actions) successful, 14 tests skipped, 1 scenarios skipped.
```

5. Test the cluster's readiness

```ctr:kubernetes
kubectl get nodes
```

```shell
NAME              STATUS   ROLES           AGE   VERSION
ip-172-31-42-53   Ready    control-plane   25m   v1.28.2
```

6. Kubernetes components
```ctr:kubernetes
kubectl get pods -n kube-system
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

