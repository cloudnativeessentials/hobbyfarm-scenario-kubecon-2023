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
[+] Building 54.2s (10/10) FINISHED                                                                                                      docker:default
 => [internal] load build definition from Dockerfile                                                                                               0.0s
 => => transferring dockerfile: 209B                                                                                                               0.0s
 => [internal] load .dockerignore                                                                                                                  0.1s
 => => transferring context: 2B                                                                                                                    0.0s
 => [internal] load metadata for docker.io/library/golang:1.21.0                                                                                   1.3s
 => [1/5] FROM docker.io/library/golang:1.21.0@sha256:b490ae1f0ece153648dd3c5d25be59a63f966b5f9e1311245c947de4506981aa                            24.3s
 => => resolve docker.io/library/golang:1.21.0@sha256:b490ae1f0ece153648dd3c5d25be59a63f966b5f9e1311245c947de4506981aa                             0.0s
 => => sha256:434a1905832963ddddafdfb5a8f329a0c0b78a3529c99d57259f0888e0feb973 7.06kB / 7.06kB                                                     0.0s
 => => sha256:de4cac68b6165c40cf6f8b30417948c31be03a968e233e55ee40221553a5e570 49.56MB / 49.56MB                                                   1.9s
 => => sha256:d31b0195ec5f04dfc78eca9d73b5d223fc36a29f54ee888bc4e0615b5839e692 24.03MB / 24.03MB                                                   1.4s
 => => sha256:12359f96c43077803959dab88ce60c87cd2543f3b907bf54a34a5104270f404f 1.58kB / 1.58kB                                                     0.0s
 => => sha256:9b1fd34c30b75e7edb20c2fd09a9862697f302ef9ae357e521ef3c84d5534e3f 64.11MB / 64.11MB                                                   3.4s
 => => sha256:b490ae1f0ece153648dd3c5d25be59a63f966b5f9e1311245c947de4506981aa 2.36kB / 2.36kB                                                     0.0s
 => => extracting sha256:de4cac68b6165c40cf6f8b30417948c31be03a968e233e55ee40221553a5e570                                                          4.5s
 => => sha256:661ee9827c7b967875eb48447f68fc66ca426a6067f385d1ce553a927a20c8ff 66.87MB / 66.87MB                                                   6.0s
 => => sha256:2f744fb50e06fe7e56d42f6f0776b4b6041b65ef9435c1982e66d5a477047682 92.27MB / 92.27MB                                                   6.3s
 => => sha256:fa5c1764d69475fa335ef99e53c3a89552eabb243701101eed02eb42d90220df 155B / 155B                                                         3.5s
 => => extracting sha256:d31b0195ec5f04dfc78eca9d73b5d223fc36a29f54ee888bc4e0615b5839e692                                                          0.9s
 => => extracting sha256:9b1fd34c30b75e7edb20c2fd09a9862697f302ef9ae357e521ef3c84d5534e3f                                                          3.9s
 => => extracting sha256:2f744fb50e06fe7e56d42f6f0776b4b6041b65ef9435c1982e66d5a477047682                                                          4.1s
 => => extracting sha256:661ee9827c7b967875eb48447f68fc66ca426a6067f385d1ce553a927a20c8ff                                                          6.9s
 => => extracting sha256:fa5c1764d69475fa335ef99e53c3a89552eabb243701101eed02eb42d90220df                                                          0.0s
 => [internal] load build context                                                                                                                  0.0s
 => => transferring context: 457B                                                                                                                  0.0s
 => [2/5] WORKDIR /app                                                                                                                             3.3s
 => [3/5] COPY *.go ./                                                                                                                             0.0s
 => [4/5] RUN go mod init simple-app                                                                                                               0.4s
 => [5/5] RUN go build -o /simple-app                                                                                                             24.0s
 => exporting to image                                                                                                                             0.6s 
 => => exporting layers                                                                                                                            0.6s 
 => => writing image sha256:47be40421214074538fb1e129da6a117c4aab58d9f249c8566daa9cc7e2454fb                                                       0.0s
 => => naming to docker.io/library/simple-app:0.1                                                                                                  0.0s
```

3. Check if the container image is available locally

```ctr:harbor
docker image ls
```

Expected output:
```shell
REPOSITORY                    TAG       IMAGE ID       CREATED              SIZE
simple-app                    0.1       c451b4bdc00a   About a minute ago   884MD
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
