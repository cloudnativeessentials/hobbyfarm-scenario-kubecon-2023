+++
title = "Kubernetes Installation: containerd"
weight = 10
+++

# Kubernetes Preqeuisities: containerd

Since Kubernetes orchestrates containers, we need a container runtime.
Kubernetes uses the Container Runtime Interface (CRI) to interface with a container runtime therefore the container runtime needs to be CRI compliant and OCI compliant. There are several to choose from, we will use containerd, a CNCF graduated project.

## Install containerd

1. Download the containerd archive from https://github.com/containerd/containerd/releases, verify its sha256sum, and extract it under `/usr/local`.

```ctr:kubernetes
wget https://github.com/containerd/containerd/releases/download/v1.7.7/containerd-1.7.7-linux-amd64.tar.gz
```

Expected output:

```shell
--2023-10-17 20:08:23--  https://github.com/containerd/containerd/releases/download/v1.7.7/containerd-1.7.7-linux-amd64.tar.gz
Resolving github.com (github.com)... 192.30.255.112
Connecting to github.com (github.com)|192.30.255.112|:443... connected.
HTTP request sent, awaiting response... 302 Found
Location: https://objects.githubusercontent.com/github-production-release-asset-2e65be/46089560/e32b0c1b-f043-4792-abbd-0751dca914a3?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20231017%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20231017T200824Z&X-Amz-Expires=300&X-Amz-Signature=c8c7774f1ce676902c51b0ca755ccf90b9c6681e56c707004cfc4bbd3dcdb503&X-Amz-SignedHeaders=host&actor_id=0&key_id=0&repo_id=46089560&response-content-disposition=attachment%3B%20filename%3Dcontainerd-1.7.7-linux-amd64.tar.gz&response-content-type=application%2Foctet-stream [following]
--2023-10-17 20:08:24--  https://objects.githubusercontent.com/github-production-release-asset-2e65be/46089560/e32b0c1b-f043-4792-abbd-0751dca914a3?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20231017%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20231017T200824Z&X-Amz-Expires=300&X-Amz-Signature=c8c7774f1ce676902c51b0ca755ccf90b9c6681e56c707004cfc4bbd3dcdb503&X-Amz-SignedHeaders=host&actor_id=0&key_id=0&repo_id=46089560&response-content-disposition=attachment%3B%20filename%3Dcontainerd-1.7.7-linux-amd64.tar.gz&response-content-type=application%2Foctet-stream
Resolving objects.githubusercontent.com (objects.githubusercontent.com)... 185.199.109.133, 185.199.110.133, 185.199.111.133, ...
Connecting to objects.githubusercontent.com (objects.githubusercontent.com)|185.199.109.133|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 46887113 (45M) [application/octet-stream]
Saving to: ‘containerd-1.7.7-linux-amd64.tar.gz’

containerd-1.7.7-linux-amd64.tar.gz   100%[=========================================================================>]  44.71M  60.0MB/s    in 0.7s    

2023-10-17 20:08:25 (60.0 MB/s) - ‘containerd-1.7.7-linux-amd64.tar.gz’ saved [46887113/46887113]
```
2. Download the sha256sum.

```ctr:kubernetes
wget https://github.com/containerd/containerd/releases/download/v1.7.7/containerd-1.7.7-linux-amd64.tar.gz.sha256sum
```

Expected output:
```shell
--2023-10-17 20:08:51--  https://github.com/containerd/containerd/releases/download/v1.7.7/containerd-1.7.7-linux-amd64.tar.gz.sha256sum
Resolving github.com (github.com)... 192.30.255.113
Connecting to github.com (github.com)|192.30.255.113|:443... connected.
HTTP request sent, awaiting response... 302 Found
Location: https://objects.githubusercontent.com/github-production-release-asset-2e65be/46089560/f802e62d-9ad5-49b7-89f8-1a32177453b6?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20231017%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20231017T200851Z&X-Amz-Expires=300&X-Amz-Signature=71bb87bceba0be7cebd9e02498eda7450cb761049afe298f77fadc003ee4984d&X-Amz-SignedHeaders=host&actor_id=0&key_id=0&repo_id=46089560&response-content-disposition=attachment%3B%20filename%3Dcontainerd-1.7.7-linux-amd64.tar.gz.sha256sum&response-content-type=application%2Foctet-stream [following]
--2023-10-17 20:08:51--  https://objects.githubusercontent.com/github-production-release-asset-2e65be/46089560/f802e62d-9ad5-49b7-89f8-1a32177453b6?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20231017%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20231017T200851Z&X-Amz-Expires=300&X-Amz-Signature=71bb87bceba0be7cebd9e02498eda7450cb761049afe298f77fadc003ee4984d&X-Amz-SignedHeaders=host&actor_id=0&key_id=0&repo_id=46089560&response-content-disposition=attachment%3B%20filename%3Dcontainerd-1.7.7-linux-amd64.tar.gz.sha256sum&response-content-type=application%2Foctet-stream
Resolving objects.githubusercontent.com (objects.githubusercontent.com)... 185.199.108.133, 185.199.109.133, 185.199.111.133, ...
Connecting to objects.githubusercontent.com (objects.githubusercontent.com)|185.199.108.133|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 102 [application/octet-stream]
Saving to: ‘containerd-1.7.7-linux-amd64.tar.gz.sha256sum’

containerd-1.7.7-linux-amd64.tar.gz.s 100%[=========================================================================>]     102  --.-KB/s    in 0s      

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

Expected output:

```shell
--2023-10-17 20:15:07--  https://github.com/opencontainers/runc/releases/download/v1.1.9/runc.amd64
Resolving github.com (github.com)... 192.30.255.113
Connecting to github.com (github.com)|192.30.255.113|:443... connected.
HTTP request sent, awaiting response... 302 Found
Location: https://objects.githubusercontent.com/github-production-release-asset-2e65be/36960321/7cb938d7-429d-41eb-9fcf-ca00f214bc12?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20231017%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20231017T201508Z&X-Amz-Expires=300&X-Amz-Signature=50cabff366af9a0762d2d9852b8d7d20e2498dd482b2f2d0a8f4cd363507d230&X-Amz-SignedHeaders=host&actor_id=0&key_id=0&repo_id=36960321&response-content-disposition=attachment%3B%20filename%3Drunc.amd64&response-content-type=application%2Foctet-stream [following]
--2023-10-17 20:15:08--  https://objects.githubusercontent.com/github-production-release-asset-2e65be/36960321/7cb938d7-429d-41eb-9fcf-ca00f214bc12?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20231017%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20231017T201508Z&X-Amz-Expires=300&X-Amz-Signature=50cabff366af9a0762d2d9852b8d7d20e2498dd482b2f2d0a8f4cd363507d230&X-Amz-SignedHeaders=host&actor_id=0&key_id=0&repo_id=36960321&response-content-disposition=attachment%3B%20filename%3Drunc.amd64&response-content-type=application%2Foctet-stream
Resolving objects.githubusercontent.com (objects.githubusercontent.com)... 185.199.109.133, 185.199.110.133, 185.199.111.133, ...
Connecting to objects.githubusercontent.com (objects.githubusercontent.com)|185.199.109.133|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 10684992 (10M) [application/octet-stream]
Saving to: ‘runc.amd64’

runc.amd64                            100%[=========================================================================>]  10.19M  --.-KB/s    in 0.1s    

2023-10-17 20:15:08 (93.0 MB/s) - ‘runc.amd64’ saved [10684992/10684992]
```

Download the asc file.

```ctr:kubernetes
wget https://github.com/opencontainers/runc/releases/download/v1.1.9/runc.amd64.asc
```

Expected output:
```shell
--2023-10-17 20:15:45--  https://github.com/opencontainers/runc/releases/download/v1.1.9/runc.amd64.asc
Resolving github.com (github.com)... 192.30.255.112
Connecting to github.com (github.com)|192.30.255.112|:443... connected.
HTTP request sent, awaiting response... 302 Found
Location: https://objects.githubusercontent.com/github-production-release-asset-2e65be/36960321/42dd1ba5-6f53-4978-8bc1-077783b21e3f?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20231017%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20231017T201545Z&X-Amz-Expires=300&X-Amz-Signature=b75c4e6eb793a0989eff956f38cdb92cb26c45dd15ba9fff58c6964f2e7dc910&X-Amz-SignedHeaders=host&actor_id=0&key_id=0&repo_id=36960321&response-content-disposition=attachment%3B%20filename%3Drunc.amd64.asc&response-content-type=application%2Foctet-stream [following]
--2023-10-17 20:15:46--  https://objects.githubusercontent.com/github-production-release-asset-2e65be/36960321/42dd1ba5-6f53-4978-8bc1-077783b21e3f?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20231017%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20231017T201545Z&X-Amz-Expires=300&X-Amz-Signature=b75c4e6eb793a0989eff956f38cdb92cb26c45dd15ba9fff58c6964f2e7dc910&X-Amz-SignedHeaders=host&actor_id=0&key_id=0&repo_id=36960321&response-content-disposition=attachment%3B%20filename%3Drunc.amd64.asc&response-content-type=application%2Foctet-stream
Resolving objects.githubusercontent.com (objects.githubusercontent.com)... 185.199.110.133, 185.199.109.133, 185.199.108.133, ...
Connecting to objects.githubusercontent.com (objects.githubusercontent.com)|185.199.110.133|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 854 [application/octet-stream]
Saving to: ‘runc.amd64.asc’

runc.amd64.asc                        100%[=========================================================================>]     854  --.-KB/s    in 0s      

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

Expected output:

```shell
disabled_plugins = []
imports = []
oom_score = 0
plugin_dir = ""
required_plugins = []
root = "/var/lib/containerd"
state = "/run/containerd"
temp = ""
version = 2

[cgroup]
  path = ""

[debug]
  address = ""
  format = ""
  gid = 0
  level = ""
  uid = 0

[grpc]
  address = "/run/containerd/containerd.sock"
  gid = 0
  max_recv_message_size = 16777216
  max_send_message_size = 16777216
  tcp_address = ""
  tcp_tls_ca = ""
  tcp_tls_cert = ""
  tcp_tls_key = ""
  uid = 0

[metrics]
  address = ""
  grpc_histogram = false

[plugins]

  [plugins."io.containerd.gc.v1.scheduler"]
    deletion_threshold = 0
    mutation_threshold = 100
    pause_threshold = 0.02
    schedule_delay = "0s"
    startup_delay = "100ms"

  [plugins."io.containerd.grpc.v1.cri"]
    cdi_spec_dirs = ["/etc/cdi", "/var/run/cdi"]
    device_ownership_from_security_context = false
    disable_apparmor = false
    disable_cgroup = false
    disable_hugetlb_controller = true
    disable_proc_mount = false
    disable_tcp_service = true
    drain_exec_sync_io_timeout = "0s"
    enable_cdi = false
    enable_selinux = false
    enable_tls_streaming = false
    enable_unprivileged_icmp = false
    enable_unprivileged_ports = false
    ignore_image_defined_volumes = false
    image_pull_progress_timeout = "1m0s"
    max_concurrent_downloads = 3
    max_container_log_line_size = 16384
    netns_mounts_under_state_dir = false
    restrict_oom_score_adj = false
    sandbox_image = "registry.k8s.io/pause:3.8"
    selinux_category_range = 1024
    stats_collect_period = 10
    stream_idle_timeout = "4h0m0s"
    stream_server_address = "127.0.0.1"
    stream_server_port = "0"
    systemd_cgroup = false
    tolerate_missing_hugetlb_controller = true
    unset_seccomp_profile = ""

    [plugins."io.containerd.grpc.v1.cri".cni]
      bin_dir = "/opt/cni/bin"
      conf_dir = "/etc/cni/net.d"
      conf_template = ""
      ip_pref = ""
      max_conf_num = 1
      setup_serially = false

    [plugins."io.containerd.grpc.v1.cri".containerd]
      default_runtime_name = "runc"
      disable_snapshot_annotations = true
      discard_unpacked_layers = false
      ignore_blockio_not_enabled_errors = false
      ignore_rdt_not_enabled_errors = false
      no_pivot = false
      snapshotter = "overlayfs"

      [plugins."io.containerd.grpc.v1.cri".containerd.default_runtime]
        base_runtime_spec = ""
        cni_conf_dir = ""
        cni_max_conf_num = 0
        container_annotations = []
        pod_annotations = []
        privileged_without_host_devices = false
        privileged_without_host_devices_all_devices_allowed = false
        runtime_engine = ""
        runtime_path = ""
        runtime_root = ""
        runtime_type = ""
        sandbox_mode = ""
        snapshotter = ""

        [plugins."io.containerd.grpc.v1.cri".containerd.default_runtime.options]

      [plugins."io.containerd.grpc.v1.cri".containerd.runtimes]

        [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc]
          base_runtime_spec = ""
          cni_conf_dir = ""
          cni_max_conf_num = 0
          container_annotations = []
          pod_annotations = []
          privileged_without_host_devices = false
          privileged_without_host_devices_all_devices_allowed = false
          runtime_engine = ""
          runtime_path = ""
          runtime_root = ""
          runtime_type = "io.containerd.runc.v2"
          sandbox_mode = "podsandbox"
          snapshotter = ""

          [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
            BinaryName = ""
            CriuImagePath = ""
            CriuPath = ""
            CriuWorkPath = ""
            IoGid = 0
            IoUid = 0
            NoNewKeyring = false
            NoPivotRoot = false
            Root = ""
            ShimCgroup = ""
            SystemdCgroup = false

      [plugins."io.containerd.grpc.v1.cri".containerd.untrusted_workload_runtime]
        base_runtime_spec = ""
        cni_conf_dir = ""
        cni_max_conf_num = 0
        container_annotations = []
        pod_annotations = []
        privileged_without_host_devices = false
        privileged_without_host_devices_all_devices_allowed = false
        runtime_engine = ""
        runtime_path = ""
        runtime_root = ""
        runtime_type = ""
        sandbox_mode = ""
        snapshotter = ""

        [plugins."io.containerd.grpc.v1.cri".containerd.untrusted_workload_runtime.options]

    [plugins."io.containerd.grpc.v1.cri".image_decryption]
      key_model = "node"

    [plugins."io.containerd.grpc.v1.cri".registry]
      config_path = ""

      [plugins."io.containerd.grpc.v1.cri".registry.auths]

      [plugins."io.containerd.grpc.v1.cri".registry.configs]

      [plugins."io.containerd.grpc.v1.cri".registry.headers]

      [plugins."io.containerd.grpc.v1.cri".registry.mirrors]

    [plugins."io.containerd.grpc.v1.cri".x509_key_pair_streaming]
      tls_cert_file = ""
      tls_key_file = ""

  [plugins."io.containerd.internal.v1.opt"]
    path = "/opt/containerd"

  [plugins."io.containerd.internal.v1.restart"]
    interval = "10s"

  [plugins."io.containerd.internal.v1.tracing"]
    sampling_ratio = 1.0
    service_name = "containerd"

  [plugins."io.containerd.metadata.v1.bolt"]
    content_sharing_policy = "shared"

  [plugins."io.containerd.monitor.v1.cgroups"]
    no_prometheus = false

  [plugins."io.containerd.nri.v1.nri"]
    disable = true
    disable_connections = false
    plugin_config_path = "/etc/nri/conf.d"
    plugin_path = "/opt/nri/plugins"
    plugin_registration_timeout = "5s"
    plugin_request_timeout = "2s"
    socket_path = "/var/run/nri/nri.sock"

  [plugins."io.containerd.runtime.v1.linux"]
    no_shim = false
    runtime = "runc"
    runtime_root = ""
    shim = "containerd-shim"
    shim_debug = false

  [plugins."io.containerd.runtime.v2.task"]
    platforms = ["linux/amd64"]
    sched_core = false

  [plugins."io.containerd.service.v1.diff-service"]
    default = ["walking"]

  [plugins."io.containerd.service.v1.tasks-service"]
    blockio_config_file = ""
    rdt_config_file = ""

  [plugins."io.containerd.snapshotter.v1.aufs"]
    root_path = ""

  [plugins."io.containerd.snapshotter.v1.blockfile"]
    fs_type = ""
    mount_options = []
    root_path = ""
    scratch_file = ""

  [plugins."io.containerd.snapshotter.v1.btrfs"]
    root_path = ""

  [plugins."io.containerd.snapshotter.v1.devmapper"]
    async_remove = false
    base_image_size = ""
    discard_blocks = false
    fs_options = ""
    fs_type = ""
    pool_name = ""
    root_path = ""

  [plugins."io.containerd.snapshotter.v1.native"]
    root_path = ""

  [plugins."io.containerd.snapshotter.v1.overlayfs"]
    mount_options = []
    root_path = ""
    sync_remove = false
    upperdir_label = false

  [plugins."io.containerd.snapshotter.v1.zfs"]
    root_path = ""

  [plugins."io.containerd.tracing.processor.v1.otlp"]
    endpoint = ""
    insecure = false
    protocol = ""

  [plugins."io.containerd.transfer.v1.local"]
    config_path = ""
    max_concurrent_downloads = 3
    max_concurrent_uploaded_layers = 3

    [[plugins."io.containerd.transfer.v1.local".unpack_config]]
      differ = ""
      platform = "linux/amd64"
      snapshotter = "overlayfs"

[proxy_plugins]

[stream_processors]

  [stream_processors."io.containerd.ocicrypt.decoder.v1.tar"]
    accepts = ["application/vnd.oci.image.layer.v1.tar+encrypted"]
    args = ["--decryption-keys-path", "/etc/containerd/ocicrypt/keys"]
    env = ["OCICRYPT_KEYPROVIDER_CONFIG=/etc/containerd/ocicrypt/ocicrypt_keyprovider.conf"]
    path = "ctd-decoder"
    returns = "application/vnd.oci.image.layer.v1.tar"

  [stream_processors."io.containerd.ocicrypt.decoder.v1.tar.gzip"]
    accepts = ["application/vnd.oci.image.layer.v1.tar+gzip+encrypted"]
    args = ["--decryption-keys-path", "/etc/containerd/ocicrypt/keys"]
    env = ["OCICRYPT_KEYPROVIDER_CONFIG=/etc/containerd/ocicrypt/ocicrypt_keyprovider.conf"]
    path = "ctd-decoder"
    returns = "application/vnd.oci.image.layer.v1.tar+gzip"

[timeouts]
  "io.containerd.timeout.bolt.open" = "0s"
  "io.containerd.timeout.metrics.shimstats" = "2s"
  "io.containerd.timeout.shim.cleanup" = "5s"
  "io.containerd.timeout.shim.load" = "5s"
  "io.containerd.timeout.shim.shutdown" = "3s"
  "io.containerd.timeout.task.state" = "2s"

[ttrpc]
  address = ""
  gid = 0
  uid = 0
```

10. Set the `systemd` cgroup driver with runc
Set the `SystemdCgroup` setting to true

```ctr:kubernetes
sudo sed -i 's@SystemdCgroup = false@SystemdCgroup = true@g' /etc/containerd/config.toml
```

11. Restart containerd

```ctr:kubernetes
sudo systemctl restart containerd
```



