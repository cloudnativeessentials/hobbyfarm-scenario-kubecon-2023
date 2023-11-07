+++
title = "Host the Container Image to Harbor"
weight = 6
+++

## Push your container image to Harbor

1. Use `docker tag` to create an image to include your Harbor instance and cloudnativeessentials project.

```ctr:harbor
docker tag simple-app:0.1 harbor.${vminfo:harbor:public_ip}.sslip.io/cloudnativeessentials/simple-app:0.1
```
2. Use `docker push` to push your container image to Harbor

```ctr:harbor
docker push harbor.${vminfo:harbor:public_ip}.sslip.io/cloudnativeessentials/simple-app:0.1
```

Expected output:
```shell
The push refers to repository [harbor.34.223.238.124.sslip.io/cloudnativeessentials/simple-app]
508125c76cbd: Pushed 
b678f20984d2: Pushed 
518b489031a7: Pushed 
19b1389586ed: Pushed 
ede2f060dece: Pushed 
fe7d12ddfc65: Pushed 
fbc321379a11: Pushed 
e51777ae0bce: Pushed 
2ef3351afa6d: Pushed 
5cc3a4df1251: Pushed 
2fa37f2ee66e: Pushed 
0.1: digest: sha256:593fca85b1a7782af61eaf78cdea99fabeebcb23d8f64fce5d91f77b120d25c8 size: 2622
```

3. Go to the Harbor instance and select the `cloudnativeessentials` project to verify the container image is present


## Pull and run your container image from Harbor

4. Let's remove the existing simple-app container images from our local VM

```ctr:harbor
docker image rm simple-app:0.1
docker image rm harbor.${vminfo:harbor:public_ip}.sslip.io/cloudnativeessentials/simple-app:0.1
```

5. Use `docker run` to pull the `simple-app:0.1` container image from your Harbor instance and run a container

```ctr:harbor
docker run --name simple-app -p 8080:8080 --detach --rm harbor.${vminfo:harbor:public_ip}.sslip.io/cloudnativeessentials/simple-app:0.1
```

6. Open a browser to test the application <a href="http://harbor.${vminfo:harbor:public_ip}.sslip.io:8080" target="_blank">http://harbor.${vminfo:harbor:public_ip}.sslip.io:8080</a>

7. Stop the `simple-app` container

```ctr:harbor
docker stop simple-app
```
