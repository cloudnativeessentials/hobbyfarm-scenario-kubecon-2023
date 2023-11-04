+++
title = "Install Harbor"
weight = 5
+++

# Harbor Installation

## Prerequisites: Docker Compose

You installed Docker in a previous step.

1. Docker Compose is a tool to run a multi-container application. The Harbor installer uses a Docker Compose file to start Harbor. Install Docker Compose:

```ctr:harbor
sudo apt install docker-compose -y
```

Expected output:
```shell
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following additional packages will be installed:
  python3-cached-property python3-docker python3-dockerpty python3-docopt python3-texttable python3-websocket
Recommended packages:
  docker.io
The following NEW packages will be installed:
  docker-compose python3-cached-property python3-docker python3-dockerpty python3-docopt python3-texttable python3-websocket
0 upgraded, 7 newly installed, 0 to remove and 108 not upgraded.
Need to get 262 kB of archives.
After this operation, 1616 kB of additional disk space will be used.
Get:1 http://us-west-2.ec2.archive.ubuntu.com/ubuntu focal/universe amd64 python3-cached-property all 1.5.1-4 [10.9 kB]
Get:2 http://us-west-2.ec2.archive.ubuntu.com/ubuntu focal/universe amd64 python3-websocket all 0.53.0-2ubuntu1 [32.3 kB]
Get:3 http://us-west-2.ec2.archive.ubuntu.com/ubuntu focal/universe amd64 python3-docker all 4.1.0-1 [83.8 kB]
Get:4 http://us-west-2.ec2.archive.ubuntu.com/ubuntu focal/universe amd64 python3-dockerpty all 0.4.1-2 [11.1 kB]
Get:5 http://us-west-2.ec2.archive.ubuntu.com/ubuntu focal/universe amd64 python3-docopt all 0.6.2-2.2ubuntu1 [19.7 kB]
Get:6 http://us-west-2.ec2.archive.ubuntu.com/ubuntu focal/universe amd64 python3-texttable all 1.6.2-2 [11.0 kB]
Get:7 http://us-west-2.ec2.archive.ubuntu.com/ubuntu focal/universe amd64 docker-compose all 1.25.0-1 [92.7 kB]
Fetched 262 kB in 0s (8457 kB/s)     
Selecting previously unselected package python3-cached-property.
(Reading database ... 62200 files and directories currently installed.)
Preparing to unpack .../0-python3-cached-property_1.5.1-4_all.deb ...
Unpacking python3-cached-property (1.5.1-4) ...
Selecting previously unselected package python3-websocket.
Preparing to unpack .../1-python3-websocket_0.53.0-2ubuntu1_all.deb ...
Unpacking python3-websocket (0.53.0-2ubuntu1) ...
Selecting previously unselected package python3-docker.
Preparing to unpack .../2-python3-docker_4.1.0-1_all.deb ...
Unpacking python3-docker (4.1.0-1) ...
Selecting previously unselected package python3-dockerpty.
Preparing to unpack .../3-python3-dockerpty_0.4.1-2_all.deb ...
Unpacking python3-dockerpty (0.4.1-2) ...
Selecting previously unselected package python3-docopt.
Preparing to unpack .../4-python3-docopt_0.6.2-2.2ubuntu1_all.deb ...
Unpacking python3-docopt (0.6.2-2.2ubuntu1) ...
Selecting previously unselected package python3-texttable.
Preparing to unpack .../5-python3-texttable_1.6.2-2_all.deb ...
Unpacking python3-texttable (1.6.2-2) ...
Selecting previously unselected package docker-compose.
Preparing to unpack .../6-docker-compose_1.25.0-1_all.deb ...
Unpacking docker-compose (1.25.0-1) ...
Setting up python3-cached-property (1.5.1-4) ...
Setting up python3-texttable (1.6.2-2) ...
Setting up python3-docopt (0.6.2-2.2ubuntu1) ...
Setting up python3-websocket (0.53.0-2ubuntu1) ...
update-alternatives: using /usr/bin/python3-wsdump to provide /usr/bin/wsdump (wsdump) in auto mode
Setting up python3-dockerpty (0.4.1-2) ...
Setting up python3-docker (4.1.0-1) ...
Setting up docker-compose (1.25.0-1) ...
Processing triggers for man-db (2.9.1-1) ...
```


## Harbor Installation

1. Download the Harbor online installer

```ctr:harbor
wget https://github.com/goharbor/harbor/releases/download/v2.7.3/harbor-online-installer-v2.7.3.tgz
```

Expected output
```shell
--2023-10-12 18:57:59--  https://github.com/goharbor/harbor/releases/download/v2.7.3/harbor-online-installer-v2.7.3.tgz
Resolving github.com (github.com)... 192.30.255.113
Connecting to github.com (github.com)|192.30.255.113|:443... connected.
HTTP request sent, awaiting response... 302 Found
Location: https://objects.githubusercontent.com/github-production-release-asset-2e65be/50613991/d934fcc0-8842-4ae4-b557-62281cfabdfc?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20231012%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20231012T185800Z&X-Amz-Expires=300&X-Amz-Signature=c84adc5fce917019d3b0e11e8a06e74d1f8ef73cca24d2e178d135f098c0286a&X-Amz-SignedHeaders=host&actor_id=0&key_id=0&repo_id=50613991&response-content-disposition=attachment%3B%20filename%3Dharbor-online-installer-v2.7.3.tgz&response-content-type=application%2Foctet-stream [following]
--2023-10-12 18:58:00--  https://objects.githubusercontent.com/github-production-release-asset-2e65be/50613991/d934fcc0-8842-4ae4-b557-62281cfabdfc?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20231012%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20231012T185800Z&X-Amz-Expires=300&X-Amz-Signature=c84adc5fce917019d3b0e11e8a06e74d1f8ef73cca24d2e178d135f098c0286a&X-Amz-SignedHeaders=host&actor_id=0&key_id=0&repo_id=50613991&response-content-disposition=attachment%3B%20filename%3Dharbor-online-installer-v2.7.3.tgz&response-content-type=application%2Foctet-stream
Resolving objects.githubusercontent.com (objects.githubusercontent.com)... 185.199.111.133, 185.199.108.133, 185.199.109.133, ...
Connecting to objects.githubusercontent.com (objects.githubusercontent.com)|185.199.111.133|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 11087 (11K) [application/octet-stream]
Saving to: ‘harbor-online-installer-v2.7.3.tgz’

harbor-online-installer-v2.7.3.tgz    100%[=========================================================================>]  10.83K  --.-KB/s    in 0s      

2023-10-12 18:58:00 (33.7 MB/s) - ‘harbor-online-installer-v2.7.3.tgz’ saved [11087/11087]

```

Download the asc file
```ctr:harbor
wget https://github.com/goharbor/harbor/releases/download/v2.7.3/harbor-online-installer-v2.7.3.tgz.asc
```
Expected output:

```shell
--2023-10-12 18:58:31--  https://github.com/goharbor/harbor/releases/download/v2.7.3/harbor-online-installer-v2.7.3.tgz.asc
Resolving github.com (github.com)... 192.30.255.113
Connecting to github.com (github.com)|192.30.255.113|:443... connected.
HTTP request sent, awaiting response... 302 Found
Location: https://objects.githubusercontent.com/github-production-release-asset-2e65be/50613991/af596d66-79ed-4dee-82fc-895b3319162b?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20231012%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20231012T185831Z&X-Amz-Expires=300&X-Amz-Signature=c3d352cee1de58c933a9caa8e3bbb49dbdb1f26e472bf0efd489f2aa004c6cfc&X-Amz-SignedHeaders=host&actor_id=0&key_id=0&repo_id=50613991&response-content-disposition=attachment%3B%20filename%3Dharbor-online-installer-v2.7.3.tgz.asc&response-content-type=application%2Foctet-stream [following]
--2023-10-12 18:58:31--  https://objects.githubusercontent.com/github-production-release-asset-2e65be/50613991/af596d66-79ed-4dee-82fc-895b3319162b?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20231012%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20231012T185831Z&X-Amz-Expires=300&X-Amz-Signature=c3d352cee1de58c933a9caa8e3bbb49dbdb1f26e472bf0efd489f2aa004c6cfc&X-Amz-SignedHeaders=host&actor_id=0&key_id=0&repo_id=50613991&response-content-disposition=attachment%3B%20filename%3Dharbor-online-installer-v2.7.3.tgz.asc&response-content-type=application%2Foctet-stream
Resolving objects.githubusercontent.com (objects.githubusercontent.com)... 185.199.109.133, 185.199.111.133, 185.199.110.133, ...
Connecting to objects.githubusercontent.com (objects.githubusercontent.com)|185.199.109.133|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 833 [application/octet-stream]
Saving to: ‘harbor-online-installer-v2.7.3.tgz.asc’

harbor-online-installer-v2.7.3.tgz.as 100%[=========================================================================>]     833  --.-KB/s    in 0s      

2023-10-12 18:58:31 (46.2 MB/s) - ‘harbor-online-installer-v2.7.3.tgz.asc’ saved [833/833]
```

2. Obtain the public key for the `asc` file

```ctr:harbor
gpg --keyserver hkps://keyserver.ubuntu.com --receive-keys 644FF454C0B4115C
```

Expected output
```shell
gpg: directory '/home/ubuntu/.gnupg' created
gpg: keybox '/home/ubuntu/.gnupg/pubring.kbx' created
gpg: /home/ubuntu/.gnupg/trustdb.gpg: trustdb created
gpg: key 644FF454C0B4115C: public key "Harbor-sign (The key for signing Harbor build) <jiangd@vmware.com>" imported
gpg: Total number processed: 1
gpg:               imported: 1
```

3. Verify the genuity of the package

The `gpg` command verifies that the bundle’s signature matches that of the *.asc key file. You should see confirmation that the signature is correct

```ctr:harbor
gpg -v --keyserver hkps://keyserver.ubuntu.com --verify harbor-online-installer-v2.7.3.tgz.asc
```

Expected output
```shell
gpg: assuming signed data in 'harbor-online-installer-v2.7.3.tgz'
gpg: Signature made Fri Sep  8 03:15:21 2023 UTC
gpg:                using RSA key 7722D168DAEC457806C96FF9644FF454C0B4115C
gpg: using pgp trust model
gpg: Good signature from "Harbor-sign (The key for signing Harbor build) <jiangd@vmware.com>" [unknown]
gpg: WARNING: This key is not certified with a trusted signature!
gpg:          There is no indication that the signature belongs to the owner.
Primary key fingerprint: 7722 D168 DAEC 4578 06C9  6FF9 644F F454 C0B4 115C
gpg: binary signature, digest algorithm SHA512, key algorithm rsa4096
```

4. Extract the installer package

```ctr:harbor
tar xzvf harbor-online-installer-v2.7.3.tgz
```

Expected output
```shell
harbor/prepare
harbor/LICENSE
harbor/install.sh
harbor/common.sh
harbor/harbor.yml.tmpl
```

## Configure HTTPS access to Harbor

You will use OpenSSL to create SSL certificates to configure HTTPS.
In the following steps, you will use OpenSSL to create a CA and use your CA to sign a server and a client certificates.
Your Harbor registry's hostname will be harbor.<public-ip>.sslip.io
In this case it will be harbor.${vminfo:harbor:public_ip}.sslip.io

1. Genereate a CA certificate private key

```ctr:harbor
openssl genrsa -out ca.key 4096
```

Expected output:
```shell
Generating RSA private key, 4096 bit long modulus (2 primes)
....++++
...............................++++
e is 65537 (0x010001)
```

2. Generate the CA certificate

```ctr:harbor
openssl req -x509 -new -nodes -sha512 -days 365 \
-subj "/C=CN/ST=Chicago/L=Chicago/O=kubecon/OU=cloudnativeessentials/CN=harbor.${vminfo:harbor:public_ip}.sslip.io" \
-key ca.key \
-out ca.crt
```

If you're curious about the `openssl req` options, checkout https://www.openssl.org/docs/man1.0.2/man1/openssl-req.html

3. Generate a Server Certificate

```ctr:harbor
openssl genrsa -out harbor.${vminfo:harbor:public_ip}.sslip.io.key 4096
```

4. Generate a certificate signing request (CSR)

```ctr:harbor
openssl req -sha512 -new \
-subj "/C=CN/ST=Chicago/L=Chicago/O=kubecon/OU=cloudnativeessentials/CN=harbor.${vminfo:harbor:public_ip}.sslip.io" \
-key harbor.${vminfo:harbor:public_ip}.sslip.io.key \
-out harbor.${vminfo:harbor:public_ip}.sslip.io.csr
```

5. Generate an x509 v3 extension file

```ctr:harbor
cat > v3.ext <<-EOF
authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
extendedKeyUsage = serverAuth
subjectAltName = @alt_names

[alt_names]
DNS.1=harbor.${vminfo:harbor:public_ip}.sslip.io
DNS.2=harbor.${vminfo:harbor:public_ip}.sslip
DNS.3=${vminfo:harbor:public_ip}
DNS.4=${vminfo:harbor:hostname}
EOF
```

6. Use the `v3.ext` file to generate a certificate for the Harbor instance

```ctr:harbor
openssl x509 -req -sha512 -days 365 \
-extfile v3.ext \
-CA ca.crt -CAkey ca.key -CAcreateserial \
-in harbor.${vminfo:harbor:public_ip}.sslip.io.csr \
-out harbor.${vminfo:harbor:public_ip}.sslip.io.crt
```

Expected output:

```shell
Certificate request self-signature ok
subject=C = CN, ST = Chicago, L = Chicago, O = kubecon, OU = cloudnativeessentials, CN = harbor.${vminfo:harbor:public_ip}.sslip.io
```

7. Copy the server certificate and key into the certificates folder

```ctr:harbor
sudo mkdir -p /data/cert/
sudo cp harbor.${vminfo:harbor:public_ip}.sslip.io.crt /data/cert/
sudo cp harbor.${vminfo:harbor:public_ip}.sslip.io.key /data/cert/
```

8. Convert your `harbor.${vminfo:harbor:public_ip}.sslip.io.crt` to `harbor.${vminfo:harbor:public_ip}.sslip.io.cert` for use by the container runtime (Docker).
`.cert` files are interpretted as client certificates while `.crt` files are interpretted as CA certificates

```ctr:harbor
openssl x509 -inform PEM -in harbor.${vminfo:harbor:public_ip}.sslip.io.crt -out harbor.${vminfo:harbor:public_ip}.sslip.io.cert
```

9. Copy the server certificate, key, and CA files into the Docker certificates folder.

```ctr:harbor
sudo mkdir -p /etc/docker/certs.d/harbor.${vminfo:harbor:public_ip}.sslip.io/
sudo cp harbor.${vminfo:harbor:public_ip}.sslip.io.cert /etc/docker/certs.d/harbor.${vminfo:harbor:public_ip}.sslip.io/
sudo cp harbor.${vminfo:harbor:public_ip}.sslip.io.key /etc/docker/certs.d/harbor.${vminfo:harbor:public_ip}.sslip.io/
sudo cp ca.crt /etc/docker/certs.d/harbor.${vminfo:harbor:public_ip}.sslip.io/
```

10. Restart the container runtime (Docker)

```ctr:harbor
sudo systemctl restart docker
```

## Configure the Harbor YAML file

1. Copy the Harbor yaml template to harbor.yml

```ctr:harbor
cp ~/harbor/harbor.yml.tmpl ~/harbor/harbor.yml
```

2. Update the Harbor yaml file with your Harbor's url
```ctr:harbor
sed -i 's@reg.mydomain.com@harbor.${vminfo:harbor:public_ip}.sslip.io@g' ~/harbor/harbor.yml
```

3. Update the Harbor yaml file with your certificate path

```ctr:harbor
sed -i 's@/your/certificate/path@/etc/docker/certs.d/harbor.${vminfo:harbor:public_ip}.sslip.io/harbor.${vminfo:harbor:public_ip}.sslip.io.cert@g' ~/harbor/harbor.yml
```

4. Update the Harbor yaml file with your private key path

```ctr:harbor
sed -i 's@/your/private/key/path@/etc/docker/certs.d/harbor.${vminfo:harbor:public_ip}.sslip.io/harbor.${vminfo:harbor:public_ip}.sslip.io.key@g' ~/harbor/harbor.yml
```

5. On Ubuntu, trust the certificate at the OS level

```ctr:harbor
sudo cp harbor.${vminfo:harbor:public_ip}.sslip.io.crt /usr/local/share/ca-certificates/harbor.${vminfo:harbor:public_ip}.sslip.io.crt
sudo update-ca-certificates
```

Expected output:
```shell
Updating certificates in /etc/ssl/certs...
rehash: warning: skipping ca-certificates.crt,it does not contain exactly one certificate or CRL
1 added, 0 removed; done.
Running hooks in /etc/ca-certificates/update.d...
done.
```

## Deploy Harbor

1. Run `prepare.sh` to enable HTTPS

```ctr:harbor
~/harbor/./prepare
```

Expected output:s
```shell
prepare base dir is set to /home/ubuntu/harbor
Unable to find image 'goharbor/prepare:v2.7.3' locally
v2.7.3: Pulling from goharbor/prepare
bb02e0e6f531: Pull complete 
15ea77c40b0c: Pull complete 
4fd4f792c862: Pull complete 
accfa41560cd: Pull complete 
240051058bfe: Pull complete 
52a85c066dfe: Pull complete 
a4b51799c095: Pull complete 
6c4a466efa5c: Pull complete 
62682b568c4b: Pull complete 
fa94b64cd7be: Pull complete 
Digest: sha256:fc012a86968d69a533c1ff7b5d448f2f805c069749f09745d4e87e80032d9076
Status: Downloaded newer image for goharbor/prepare:v2.7.3
Generated configuration file: /config/portal/nginx.conf
Generated configuration file: /config/log/logrotate.conf
Generated configuration file: /config/log/rsyslog_docker.conf
Generated configuration file: /config/nginx/nginx.conf
Generated configuration file: /config/core/env
Generated configuration file: /config/core/app.conf
Generated configuration file: /config/registry/config.yml
Generated configuration file: /config/registryctl/env
Generated configuration file: /config/registryctl/config.yml
Generated configuration file: /config/db/env
Generated configuration file: /config/jobservice/env
Generated configuration file: /config/jobservice/config.yml
Generated and saved secret to file: /data/secret/keys/secretkey
Successfully called func: create_root_cert
Generated configuration file: /compose_location/docker-compose.yml
Clean up the input dir

```

2. Run `install.sh`
```ctr:harbor
sudo ~/harbor/./install.sh
```

Expected output:
```shell
[Step 0]: checking if docker is installed ...

Note: docker version: 24.0.6

[Step 1]: checking docker-compose is installed ...

Note: Docker Compose version v2.21.0


[Step 2]: preparing environment ...

[Step 3]: preparing harbor configs ...
prepare base dir is set to /home/ubuntu/harbor
Clearing the configuration file: /config/registryctl/config.yml
...
[+] Running 10/10
 ✔ Network harbor_harbor        Created                                                                                                            0.1s 
 ✔ Container harbor-log         Started                                                                                                            1.5s 
 ✔ Container harbor-db          Started                                                                                                            0.1s 
 ✔ Container redis              Started                                                                                                            0.1s 
 ✔ Container registry           Started                                                                                                            0.1s 
 ✔ Container harbor-portal      Started                                                                                                            0.1s 
 ✔ Container registryctl        Started                                                                                                            0.1s 
 ✔ Container harbor-core        Started                                                                                                            0.0s 
 ✔ Container harbor-jobservice  Started                                                                                                            0.0s 
 ✔ Container nginx              Started                                                                                                            0.1s 
✔ ----Harbor has been installed and started successfully.----
```

3. Check the Harbor containers 
```ctr:harbor
docker ps
```

Expected output:
```shell
CONTAINER ID   IMAGE                                COMMAND                  CREATED              STATUS                    PORTS                                                                            NAMES
bde32f01e01a   goharbor/harbor-jobservice:v2.7.3    "/harbor/entrypoint.…"   57 seconds ago       Up 48 seconds (healthy)                                                                                    harbor-jobservice
9c08d0ebaff5   goharbor/nginx-photon:v2.7.3         "nginx -g 'daemon of…"   57 seconds ago       Up 53 seconds (healthy)   0.0.0.0:80->8080/tcp, :::80->8080/tcp, 0.0.0.0:443->8443/tcp, :::443->8443/tcp   nginx
14d6a81db604   goharbor/harbor-core:v2.7.3          "/harbor/entrypoint.…"   57 seconds ago       Up 54 seconds (healthy)                                                                                    harbor-core
feddcf259078   goharbor/harbor-db:v2.7.3            "/docker-entrypoint.…"   57 seconds ago       Up 55 seconds (healthy)                                                                                    harbor-db
5c125bfedb63   goharbor/registry-photon:v2.7.3      "/home/harbor/entryp…"   57 seconds ago       Up 55 seconds (healthy)                                                                                    registry
8ec76866659b   goharbor/redis-photon:v2.7.3         "redis-server /etc/r…"   57 seconds ago       Up 55 seconds (healthy)                                                                                    redis
9f60993c55c9   goharbor/harbor-portal:v2.7.3        "nginx -g 'daemon of…"   57 seconds ago       Up 55 seconds (healthy)                                                                                    harbor-portal
eaa338023837   goharbor/harbor-registryctl:v2.7.3   "/home/harbor/start.…"   57 seconds ago       Up 55 seconds (healthy)                                                                                    registryctl
98e7c9694a13   goharbor/harbor-log:v2.7.3           "/bin/sh -c /usr/loc…"   About a minute ago   Up 56 seconds (healthy)   127.0.0.1:1514->10514/tcp                                                        harbor-log
```

4. 

