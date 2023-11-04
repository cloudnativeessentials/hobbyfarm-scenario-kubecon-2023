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
FROM golang:1.21-alpine3.17
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
[+] Building 35.1s (10/10) FINISHED                                                                                                      docker:default
 => [internal] load build definition from Dockerfile                                                                                               0.0s
 => => transferring dockerfile: 218B                                                                                                               0.0s
 => [internal] load .dockerignore                                                                                                                  0.0s
 => => transferring context: 2B                                                                                                                    0.0s
 => [internal] load metadata for docker.io/library/golang:1.21-alpine3.17                                                                          1.1s
 => [1/5] FROM docker.io/library/golang:1.21-alpine3.17@sha256:6e069c2467a974f2b77ebee687d820d864e9632d2c606bd7e8478b2be437678e                    8.2s
 => => resolve docker.io/library/golang:1.21-alpine3.17@sha256:6e069c2467a974f2b77ebee687d820d864e9632d2c606bd7e8478b2be437678e                    0.0s
 => => sha256:a1ef7ce3344464e5090f7a329df2dfd9bb5025b01275806248dba373a82c2413 284.93kB / 284.93kB                                                 0.2s
 => => sha256:da8cd8fd6e936ae489ee166bf78cbf91a29edeaa0bf7ae97fa2314c7cca723a8 67.02MB / 67.02MB                                                   1.1s
 => => sha256:6e069c2467a974f2b77ebee687d820d864e9632d2c606bd7e8478b2be437678e 1.65kB / 1.65kB                                                     0.0s
 => => sha256:06cb1dae8604f1655f089bb3dd210a8123e0c9deaca78033f4629979ed316337 1.16kB / 1.16kB                                                     0.0s
 => => sha256:a9912e40f99f02b22dc040191ae0d85fc96cb4389a8ba9b81657cad47497d818 6.32kB / 6.32kB                                                     0.0s
 => => sha256:9398808236ffac29e60c04ec906d8d409af7fa19dc57d8c65ad167e9c4967006 3.38MB / 3.38MB                                                     0.2s
 => => extracting sha256:9398808236ffac29e60c04ec906d8d409af7fa19dc57d8c65ad167e9c4967006                                                          0.2s
 => => sha256:17e1d6a3935137abe8d550b4a284cdd9887a117482e27a1ac714d4993d71d501 155B / 155B                                                         0.3s
 => => extracting sha256:a1ef7ce3344464e5090f7a329df2dfd9bb5025b01275806248dba373a82c2413                                                          0.1s
 => => extracting sha256:da8cd8fd6e936ae489ee166bf78cbf91a29edeaa0bf7ae97fa2314c7cca723a8                                                          6.6s
 => => extracting sha256:17e1d6a3935137abe8d550b4a284cdd9887a117482e27a1ac714d4993d71d501                                                          0.0s
 => [internal] load build context                                                                                                                  0.0s
 => => transferring context: 29B                                                                                                                   0.0s
 => [2/5] WORKDIR /app                                                                                                                             1.1s
 => [3/5] COPY *.go ./                                                                                                                             0.0s
 => [4/5] RUN go mod init simple-app                                                                                                               0.3s
 => [5/5] RUN go build -o /simple-app                                                                                                             23.6s
 => exporting to image                                                                                                                             0.7s
 => => exporting layers                                                                                                                            0.7s
 => => writing image sha256:f261baa9126a6e0fca3bdf9c87ee8d4d5e5f0d8147aa39270bcc23bf9a1b0e90                                                       0.0s
 => => naming to docker.io/library/simple-app:0.1                                                                                                  0.0s
```

3. Check if the container image is available locally

```ctr:harbor
docker image ls
```

Expected output:

```shell
REPOSITORY                    TAG       IMAGE ID       CREATED              SIZE
simple-app                    0.1       c451b4bdc00a   About a minute ago   291MB
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
