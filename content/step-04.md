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
FROM golang:1.21.3
LABEL project=cloudnativeessentials
WORKDIR /app
COPY go.mod ./
RUN go mod download
COPY *.go ./
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
[+] Building 1.1s (11/11) FINISHED                                                                                                       docker:default
 => [internal] load build definition from Dockerfile                                                                                               0.0s
 => => transferring dockerfile: 217B                                                                                                               0.0s
 => [internal] load .dockerignore                                                                                                                  0.0s
 => => transferring context: 2B                                                                                                                    0.0s
 => [internal] load metadata for docker.io/library/golang:1.21.3                                                                                   0.3s
 => [1/6] FROM docker.io/library/golang:1.21.3@sha256:b113af1e8b06f06a18ad41a6b331646dff587d7a4cf740f4852d16c49ed8ad73                             0.0s
 => [internal] load build context                                                                                                                  0.0s
 => => transferring context: 54B                                                                                                                   0.0s
 => CACHED [2/6] WORKDIR /app                                                                                                                      0.0s
 => CACHED [3/6] COPY go.mod ./                                                                                                                    0.0s
 => CACHED [4/6] RUN go mod download                                                                                                               0.0s
 => CACHED [5/6] COPY *.go ./                                                                                                                      0.0s
 => CACHED [6/6] RUN go build -o /simple-app                                                                                                       0.0s
 => exporting to image                                                                                                                             0.8s
 => => exporting layers                                                                                                                            0.8s
 => => writing image sha256:c6f0d3b9fd288da34804bf4b17dcd6b852c59f2d19804fcbb9b7d06643cdcc66                                                       0.0s
 => => naming to docker.io/library/simple-app:0.1                                                                                                  0.0s
```

3. Check if the container image is available locally
```ctr:harbor
docker image ls
```

Expected output:
```shell
REPOSITORY                    TAG       IMAGE ID       CREATED              SIZE
simple-app                    0.1       c451b4bdc00a   About a minute ago   885MB
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
