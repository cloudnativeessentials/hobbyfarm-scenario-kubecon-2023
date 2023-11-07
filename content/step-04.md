+++
title = "Containerize an Application: Creating a Container Image"
weight = 4
+++

Now we have a simple application, let's containerize it using Docker tools. The `docker build` command creates container images using a Dockerfile.
A Dockerfile is a text file with the commands to assemble a container image with a format of `INSTRUCTION argument`. 
Each Dockerfile starts with the `FROM` instruction for the base container image to base the container image from.

<br/>

1. Create a Dockerfile for the `simple-app`.

```file:dockerfile:~/simple-app/Dockerfile:harbor
FROM golang:1.21-alpine3.17
LABEL project=cloudnativeessentials
WORKDIR /app
COPY *.go ./
RUN go mod init simple-app
RUN go build -o /simple-app
EXPOSE 8080
CMD [ "/simple-app" ]
```

2. Use `docker build` to create the simple-app:0.1 container image.

```ctr:harbor
docker build -t simple-app:0.1 ~/simple-app/.
```

A significant amount of output should appear. When the process has completed you should see:

```shell
 => => naming to docker.io/library/simple-app:0.1                                                                                                  0.0s
```

<br/>

3. Check if the container image is available locally.

```ctr:harbor
docker image ls
```

Expected output:

```shell
REPOSITORY                    TAG       IMAGE ID       CREATED              SIZE
simple-app                    0.1       c451b4bdc00a   About a minute ago   291MB
```

4. Run a container from the new `simple-app:0.1` container image.

```ctr:harbor
docker run --name simple-app -p 8080:8080 --detach --rm simple-app:0.1
```

5. Open a browser to test the application.
<a href="http://harbor.${vminfo:harbor:public_ip}.sslip.io:8080" target="_blank">http://harbor.${vminfo:harbor:public_ip}.sslip.io:8080</a>


6. Stop the `simple-app` container.

```ctr:harbor
docker stop simple-app
```
