+++
title = "Accessing Harbor"
weight = 6
+++

1. Log into Harbor from the Docker client
```ctr:harbor
docker login harbor.${vminfo:harbor:public_ip}.sslip.io
```

Use the default admin credentials to login:
   username: `admin`
   password: `Harbor12345`

Expected output:
```shell
Username: admin
Password: 
WARNING! Your password will be stored unencrypted in /home/ubuntu/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
```

2. Access Harbor at <a href="https://harbor.${vminfo:harbor:public_ip}.sslip.io">https://harbor.${vminfo:harbor:public_ip}.sslip.io</a>
Some browsers might show a warning stating that the Certificate Authority (CA) is unknown. This happens when using a self-signed CA that is not from a trusted third-party CA. You can import the CA to the browser to remove the warning. You can skip this warning. Some Chromium based browsers may not show a skip button. If this is the case, just click anywhere on the error page and type "thisisunsafe" (without quotes). This will force the browser to bypass the warning and accept the certificate.
3. Use the default admin credentials:
   username: `admin`
   password: `Harbor12345`
4. Create a new project, give it the name `cloudnativeessentials` and use the defaults

