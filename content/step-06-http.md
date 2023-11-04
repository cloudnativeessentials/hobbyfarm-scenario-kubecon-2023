+++
title = "Accessing Harbor"
weight = 6
+++

1. Setup Docker to use an insecure registry

Create a `/etc/docker/daemon.json`

```ctr:harbor
sudo vi /etc/docker/daemon.json
```

Press `i` for insert mode and enter:

```shell
{
    "insecure-registries" : [ "harbor.${vminfo:harbor:public_ip}.sslip.io" ]
}
```

To save: `esc:wq` 

2. Restart Docker

```ctr:harbor
sudo systemctl daemon-reload
sudo systemctl restart docker
```

3. Log into Harbor from the Docker client

```ctr:harbor
docker login harbor.${vminfo:harbor:public_ip}.sslip.io
```

Use the default admin credentials to login:
   username: `admin`
   password: `Harbor12345`

Expected output:
```shell
WARNING! Your password will be stored unencrypted in /home/ubuntu/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
```

4. Access Harbor at <a href="http://harbor.${vminfo:harbor:public_ip}.sslip.io">http://harbor.${vminfo:harbor:public_ip}.sslip.io</a>
Some browsers might show a warning stating that the Certificate Authority (CA) is unknown. This happens when using a self-signed CA that is not from a trusted third-party CA. You can import the CA to the browser to remove the warning. You can skip this warning. Some Chromium based browsers may not show a skip button. If this is the case, just click anywhere on the error page and type "thisisunsafe" (without quotes). This will force the browser to bypass the warning and accept the certificate.

5. Use the default admin credentials:
   username: `admin`
   password: `Harbor12345`

6. Create a new project, give it the name `cloudnativeessentials`, set it for public and use the defaults for the rest.

