+++
title = "Containerize an Application: Docker Install"
weight = 2
+++

The first step on the Cloud Native Trail Map is Containerization. Let's install Docker, a OCI-compliant container runtime.

Although Docker is not a CNCF project, it was the project that popularized containers. Docker and Docker Compose are required by the Harbor installation in later steps.

## Install Docker

1. Run the Docker installer.

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

3. Add user to the docker group.

```ctr:harbor
sudo usermod -aG docker $USER
```

4. Activate changes to the docker group.

```ctr:harbor
newgrp docker
```

5. Verify Docker is running.

```ctr:harbor
docker version
```

