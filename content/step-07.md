+++
title = "Kubernetes Requirements"
weight = 7
+++

# Kubernetes Requirements: cgroups v2, iptables, socat, conntrack

## cgroups v2

Starting in Kubernetes 1.28, Kubernetes can leverage cgroups v2 for enhanced resource management capabilities including swap.
If cgroups v1 is used then you would have to disable swap with `sudo swapoff -a` and disable swap in `/etc/fstab` or `systemd.swap`.


1. Check if cgroups v2 is used.
   
```ctr:kubernetes
stat -fc %T /sys/fs/cgroup/
```

Expected output:

```shell
cgroup2fs
```

If the output is `tempfs` then cgroups v1 is used. (You shouldn't see this).

---

## Forward IPv4 and iptables bridged traffic

2. Forwarding IPv4 and letting iptables see bridged traffic.
Load the `overlay` and `br_netfilter` kernel modules in the current environment and set them to load on boot

```ctr:kubernetes
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF
```

3. The `modprobe` command can add or remove kernel modules. Use `modprobe` to add the `overlay` and `br_netfilter` kernel modules.
The `br_netfilter` module enables transparent masquerading and facilitate Virtual Extensible LAN (VxLAN) traffic for communication between Kubernetes pods.

```ctr:kubernetes
sudo modprobe -a overlay br_netfilter
```

4. Configure sysctl parameters to persist reboots

```ctr:kubernetes
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF
```

5. Apply sysctl parameters without rebooting

```ctr:kubernetes
sudo sysctl --system
```

The expected output should contain many sysctl parameters, and should end with:

```shell
* Applying /etc/sysctl.conf ...
```

6. Verify that the br_netfilter, overlay modules are loaded

```ctr:kubernetes
lsmod | grep br_netfilter && lsmod | grep overlay
```

Expected output:

```shell
br_netfilter           32768  0
bridge                331776  1 br_netfilter
overlay               159744  0
```

7. Verify that the net.bridge.bridge-nf-call-iptables, net.bridge.bridge-nf-call-ip6tables, and net.ipv4.ip_forward system variables are set to 1 in your sysctl config by running the following command:

```ctr:kubernetes
sysctl net.bridge.bridge-nf-call-iptables net.bridge.bridge-nf-call-ip6tables net.ipv4.ip_forward
```

Expected output:

```shell
net.bridge.bridge-nf-call-iptables = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward = 1
```

8. Install necessary software

`socat` helps redirect traffic within the Kubernetes cluster. `conntrack` helps connection information 
between Pods and Services.

```ctr:kubernetes
sudo apt install socat conntrack -y
```