+++
title = "Install Harbor"
weight = 5
+++

### Prerequisites: Docker Compose and Docker Insecure Registry

You installed Docker in a previous step.

<br/>

1. Docker Compose is a tool to run a multi-container application. The Harbor installer uses a Docker Compose file to start Harbor. 

Install Docker Compose:

```ctr:harbor
sudo apt install docker-compose -y
```

<br/>

2. In following steps, we will setup Harbor to use http and not https. Docker requires configuration to use insecure registries.

Setup Docker to use an insecure registry by creating an `insecure-registries` entry into `/etc/docker/daemon.json`

Create a `/etc/docker/daemon.json`

```ctr:harbor
sudo tee /etc/docker/daemon.json &>/dev/null <<EOF
{
    "insecure-registries": ["harbor.${vminfo:harbor:public_ip}.sslip.io"]
}
EOF
```

<br/>

3. Restart Docker

```ctr:harbor
sudo systemctl daemon-reload && sudo systemctl restart docker
```

## Harbor Installation

4. Download the Harbor online installer

```ctr:harbor
wget https://github.com/goharbor/harbor/releases/download/v2.7.3/harbor-online-installer-v2.7.3.tgz
```

Expected output ends with:

```shell
2023-10-12 18:58:00 (33.7 MB/s) - ‘harbor-online-installer-v2.7.3.tgz’ saved [11087/11087]
```

<br/>

5. Download the asc file

```ctr:harbor
wget https://github.com/goharbor/harbor/releases/download/v2.7.3/harbor-online-installer-v2.7.3.tgz.asc
```

Expected output ends with:

```shell
2023-10-12 18:58:31 (46.2 MB/s) - ‘harbor-online-installer-v2.7.3.tgz.asc’ saved [833/833]
```

<br/>

6. Obtain the public key for the `asc` file

```ctr:harbor
gpg --keyserver hkps://keyserver.ubuntu.com --receive-keys 644FF454C0B4115C
```

Expected output ends with:

```shell
gpg:               imported: 1
```

<br/>

7. Verify the genuity of the package

The `gpg` command verifies that the bundle’s signature matches that of the *.asc key file. You should see confirmation that the signature is correct

```ctr:harbor
gpg -v --keyserver hkps://keyserver.ubuntu.com --verify harbor-online-installer-v2.7.3.tgz.asc
```

Expected output ends with:

```shell
gpg: binary signature, digest algorithm SHA512, key algorithm rsa4096
```

<br/>

8. Extract the installer package

```ctr:harbor
tar xzvf harbor-online-installer-v2.7.3.tgz
```

Expected output:

```shell
harbor/prepare
harbor/LICENSE
harbor/install.sh
harbor/common.sh
harbor/harbor.yml.tmpl
```

## Configure the Harbor YAML file

9. Copy the Harbor yaml template to harbor.yml

```ctr:harbor
cp ~/harbor/harbor.yml.tmpl ~/harbor/harbor.yml
```

10. Update the Harbor yaml file with your Harbor's url, as well as disable HTTPS

```ctr:harbor
sed -i 's@reg.mydomain.com@harbor.${vminfo:harbor:public_ip}.sslip.io@g' ~/harbor/harbor.yml

sed -i '13,18s/^/#/g' ~/harbor/harbor.yml
```

## Deploy Harbor

11. Run `install.sh`
```ctr:harbor
sudo ~/harbor/./install.sh
```

Expected output should end with:
```shell
✔ ----Harbor has been installed and started successfully.----
```

12. Check the Harbor containers 
```ctr:harbor
docker ps
```

The expected output from this command is a text table showing all containers running on the node. 
The containers shown should all have "Up xx seconds" or "Up xx minutes" as their status.

</br/>

13. Log into Harbor from the Docker client

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

14. Access Harbor at <a href="http://harbor.${vminfo:harbor:public_ip}.sslip.io">http://harbor.${vminfo:harbor:public_ip}.sslip.io</a>
Some browsers might show a warning stating that the Certificate Authority (CA) is unknown. This happens when using a self-signed CA that is not from a trusted third-party CA. You can import the CA to the browser to remove the warning. You can skip this warning. Some Chromium based browsers may not show a skip button. If this is the case, just click anywhere on the error page and type "thisisunsafe" (without quotes). This will force the browser to bypass the warning and accept the certificate.

15. Login using the default admin credentials:

   username: `admin`
   password: `Harbor12345`

16. From the home page, click on the "New Project" button in the middle. Name the new project `cloudnativeessentials`, check the box labeled "Public" and click "OK".