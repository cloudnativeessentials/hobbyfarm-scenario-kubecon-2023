+++
title = "Push Your Container Image to Harbor"
weight = 7
+++

# Push your container image to Harbor

1. Use `docker tag` to create an image to include your Harbor instance and cloudnativeessentials project.
```ctr:harbor
docker tag simple-app:0.1 harbor.${vminfo:harbor:public_ip}.sslip.io/cloudnativeessentials/simple-app:0.2
```
2. Use `docker push` to push your container image to Harbor

```ctr:harbor
docker push harbor.${vminfo:harbor:public_ip}.sslip.io/cloudnativeessentials/simple-app:0.2
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

3. Go to the Harbor instance and select the cloudnativeessentials project to verify the container image is present

