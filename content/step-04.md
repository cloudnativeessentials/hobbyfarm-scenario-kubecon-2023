+++
title = "Containerize an Application: Creating a Container Image"
weight = 4
+++

Now we have a simple application, let's containerize it using Docker tools.
The `docker build` command creates container images using a Dockerfile.
A Dockerfile is a text file with the commands to assemble a container image with a format of `INSTRUCTION argument`
Each Dockerfile starts with the `FROM` instruction.

1. Create a Dockerfile for the simple app
```ctr:harbor
cat <<EOF > ~/simple-app/Dockerfile
FROM golang:1.21.0
LABEL project=cloudnativeessentials
WORKDIR /app
COPY *.go ./
RUN go mod init simple-app
RUN go build -o /simple-app
EXPOSE 8080
CMD [ "/simple-app" ]
EOF
```

2. Use `docker build` to create the simple-app:0.1 container image
```ctr:harbor
docker build -t simple-app:0.1 ~/simple-app/.
```

Expected output:
```shell
[+] Building 0.4s (7/7) FINISHED                                                                                                         docker:default
 => [internal] load build definition from Dockerfile                                                                                               0.0s
 => => transferring dockerfile: 138B                                                                                                               0.0s
 => [internal] load .dockerignore                                                                                                                  0.0s
 => => transferring context: 2B                                                                                                                    0.0s
 => [internal] load metadata for docker.io/library/golang:1.21.3                                                                                   0.3s
 => [internal] load build context                                                                                                                  0.0s
 => => transferring context: 54B                                                                                                                   0.0s
 => [1/2] FROM docker.io/library/golang:1.21.3@sha256:b113af1e8b06f06a18ad41a6b331646dff587d7a4cf740f4852d16c49ed8ad73                             0.0s
 => CACHED [2/2] COPY *.* ./                                                                                                                       0.0s
 => exporting to image                                                                                                                             0.0s
 => => exporting layers                                                                                                                            0.0s
 => => writing image sha256:dc328b06365405a90dedced916b47f5a3e3c4c19fdd57ae5794755d9f309ca7e                                                       0.0s
 => => naming to docker.io/library/simple-app:0.1                                                                                                  0.0s
```

3. Check if the container image is available locally
```ctr:harbor
docker image ls
```

Expected output:
```shell
REPOSITORY                    TAG       IMAGE ID       CREATED              SIZE
simple-app                    0.1       c451b4bdc00a   About a minute ago   815MB
```

4. Run a container from the new `simple-app:0.1` container image
```ctr:harbor
docker run --name simple-app -p 8080:8080 --detach --rm simple-app:0.1
```

5. Open a browser to test the application
<a href="http://harbor.${vminfo:harbor:public_ip}.sslip.io:8080" target="_blank">http://harbor.${vminfo:harbor:public_ip}.sslip.io:8080</a>


6. Stop the `simple-app` container
```ctr:harbor
docker stop simple-app
```
