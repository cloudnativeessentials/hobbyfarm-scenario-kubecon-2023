+++
title = "Containerizing an Application"
weight = 2
+++

The first step on the Cloud Native Trail Map is Containerization. Let's install Docker, a OCI-compliant container runtime.

Although Docker is not a CNCF project, it was the project that popularized containers. Docker and Docker Compose are required by the Harbor installation in later steps.

## Install Docker

1. Install Docker

```ctr:harbor
sudo mkdir -p /etc/docker
sudo apt-get update
```

Exptected output:
```shell
Hit:1 http://us-west-2.ec2.archive.ubuntu.com/ubuntu jammy InRelease
Get:2 http://us-west-2.ec2.archive.ubuntu.com/ubuntu jammy-updates InRelease [119 kB]
Get:3 http://us-west-2.ec2.archive.ubuntu.com/ubuntu jammy-backports InRelease [109 kB]
...
Get:42 http://security.ubuntu.com/ubuntu jammy-security/multiverse amd64 c-n-f Metadata [260 B]
Fetched 27.7 MB in 4s (6249 kB/s)               
Reading package lists... Done
```

Run Docker installer
```ctr:harbor
wget -qO- https://get.docker.com/ | sh
```

Expected output:
```shell
# Executing docker install script, commit: e5543d473431b782227f8908005543bb4389b8de
+ sudo -E sh -c apt-get update -qq >/dev/null
+ sudo -E sh -c DEBIAN_FRONTEND=noninteractive apt-get install -y -qq apt-transport-https ca-certificates curl >/dev/null
...
```

3. Add user to the docker group

```ctr:harbor
sudo usermod -aG docker $USER
```

4. Activate changes to the docker group

```ctr:harbor
newgrp docker
```

5. Verify Docker is running
```ctr:harbor
docker version
```

