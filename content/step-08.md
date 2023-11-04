+++
title = "Pull and Run Your Container Image from Harbor"
weight = 8
+++

# Pull and run your container image from Harbor

1. Let's remove the existing simple-app container images from our local VM

```ctr:harbor
docker image rm simple-app:0.1
docker image rm harbor.${vminfo:harbor:public_ip}.sslip.io/cloudnativeessentials/simple-app:0.1
```

2. Use `docker run` to pull the `simple-app:0.1` container image from your Harbor instance and run a container

```ctr:harbor
docker run --name simple-app -p 8080:8080 --detach --rm harbor.${vminfo:harbor:public_ip}.sslip.io/cloudnativeessentials/simple-app:0.1
```

3. Open a browser to test the application <a href="http://harbor.${vminfo:harbor:public_ip}.sslip.io:8080" target="_blank">http://harbor.${vminfo:harbor:public_ip}.sslip.io:8080</a>

4. Stop the `simple-app` container

```ctr:harbor
docker stop simple-app
```

