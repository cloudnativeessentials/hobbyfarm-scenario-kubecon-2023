+++
title = "Kubernetes Requirements"
weight = 9
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

```
cgroup2fs
```

If the output is `tempfs` then cgroups v1 is used.

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
sudo modprobe overlay
sudo modprobe br_netfilter
```

4. Configure sysctl parameters to persist reboots

```ctr:kuberentes
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

Expected output:
```shell
* Applying /etc/sysctl.d/10-console-messages.conf ...
kernel.printk = 4 4 1 7
* Applying /etc/sysctl.d/10-ipv6-privacy.conf ...
net.ipv6.conf.all.use_tempaddr = 2
net.ipv6.conf.default.use_tempaddr = 2
* Applying /etc/sysctl.d/10-kernel-hardening.conf ...
kernel.kptr_restrict = 1
* Applying /etc/sysctl.d/10-magic-sysrq.conf ...
kernel.sysrq = 176
* Applying /etc/sysctl.d/10-network-security.conf ...
net.ipv4.conf.default.rp_filter = 2
net.ipv4.conf.all.rp_filter = 2
* Applying /etc/sysctl.d/10-ptrace.conf ...
kernel.yama.ptrace_scope = 1
* Applying /etc/sysctl.d/10-zeropage.conf ...
vm.mmap_min_addr = 65536
* Applying /usr/lib/sysctl.d/50-default.conf ...
kernel.core_uses_pid = 1
net.ipv4.conf.default.rp_filter = 2
net.ipv4.conf.default.accept_source_route = 0
sysctl: setting key "net.ipv4.conf.all.accept_source_route": Invalid argument
net.ipv4.conf.default.promote_secondaries = 1
sysctl: setting key "net.ipv4.conf.all.promote_secondaries": Invalid argument
net.ipv4.ping_group_range = 0 2147483647
net.core.default_qdisc = fq_codel
fs.protected_hardlinks = 1
fs.protected_symlinks = 1
fs.protected_regular = 1
fs.protected_fifos = 1
* Applying /usr/lib/sysctl.d/50-pid-max.conf ...
kernel.pid_max = 4194304
* Applying /etc/sysctl.d/99-cloudimg-ipv6.conf ...
net.ipv6.conf.all.use_tempaddr = 0
net.ipv6.conf.default.use_tempaddr = 0
* Applying /usr/lib/sysctl.d/99-protect-links.conf ...
fs.protected_fifos = 1
fs.protected_hardlinks = 1
fs.protected_regular = 2
fs.protected_symlinks = 1
* Applying /etc/sysctl.d/99-sysctl.conf ...
* Applying /etc/sysctl.d/k8s.conf ...
net.bridge.bridge-nf-call-iptables = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward = 1
* Applying /etc/sysctl.conf ...
```

6. Verify that the br_netfilter, overlay modules are loaded

```ctr:kubernetes
lsmod | grep br_netfilter
lsmod | grep overlay
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

8. socat helps redirect traffic within the Kubernetes cluster. Install socat

```ctr:kubernetes
sudo apt install socat -y
```

Expected output:

```shell
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following NEW packages will be installed:
  socat
0 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
Need to get 349 kB of archives.
After this operation, 1381 kB of additional disk space will be used.
Get:1 http://us-west-2.ec2.archive.ubuntu.com/ubuntu jammy/main amd64 socat amd64 1.7.4.1-3ubuntu4 [349 kB]
Fetched 349 kB in 0s (18.2 MB/s)
Selecting previously unselected package socat.
(Reading database ... 64295 files and directories currently installed.)
Preparing to unpack .../socat_1.7.4.1-3ubuntu4_amd64.deb ...
Unpacking socat (1.7.4.1-3ubuntu4) ...
Setting up socat (1.7.4.1-3ubuntu4) ...
Processing triggers for man-db (2.10.2-1) ...
Scanning processes...                                                                                                                                   
Scanning linux images...                                                                                                                                

Running kernel seems to be up-to-date.

No services need to be restarted.

No containers need to be restarted.

No user sessions are running outdated binaries.

No VM guests are running outdated hypervisor (qemu) binaries on this host.
```

8. conntrack helps connection information between Pods and Services. Install conntrack

```ctr:kubernetes
sudo apt install conntrack -y
```

Expected output:

```shell
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following NEW packages will be installed:
  conntrack
0 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
Need to get 33.5 kB of archives.
After this operation, 104 kB of additional disk space will be used.
Get:1 http://us-west-2.ec2.archive.ubuntu.com/ubuntu jammy/main amd64 conntrack amd64 1:1.4.6-2build2 [33.5 kB]
Fetched 33.5 kB in 0s (1198 kB/s)
Selecting previously unselected package conntrack.
(Reading database ... 64335 files and directories currently installed.)
Preparing to unpack .../conntrack_1%3a1.4.6-2build2_amd64.deb ...
Unpacking conntrack (1:1.4.6-2build2) ...
Setting up conntrack (1:1.4.6-2build2) ...
Processing triggers for man-db (2.10.2-1) ...
Scanning processes...                                                                                                                                   
Scanning linux images...                                                                                                                                

Running kernel seems to be up-to-date.

No services need to be restarted.

No containers need to be restarted.

No user sessions are running outdated binaries.

No VM guests are running outdated hypervisor (qemu) binaries on this host.
```

