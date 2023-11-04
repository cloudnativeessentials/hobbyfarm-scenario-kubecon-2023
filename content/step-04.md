+++
title = "Containerize an Application: Creating a Container Image"
weight = 4
+++

Now we have a simple application, let's containerize it using the Docker tools.
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

2. Use `docker build` to create the container image
```ctr:harbor
docker build -t simple-app:0.1 ~/simple-app/.
```

Expected output:
```shell
[+] Building 50.8s (11/11) FINISHED                                                                                                      docker:default
 => [internal] load build definition from Dockerfile                                                                                               0.1s
 => => transferring dockerfile: 221B                                                                                                               0.0s
 => [internal] load .dockerignore                                                                                                                  0.1s
 => => transferring context: 2B                                                                                                                    0.0s
 => [internal] load metadata for docker.io/library/golang:1.21.3                                                                                   1.1s
 => [1/6] FROM docker.io/library/golang:1.21.3@sha256:24a09375a6216764a3eda6a25490a88ac178b5fcb9511d59d0da5ebf9e496474                            22.1s
 => => resolve docker.io/library/golang:1.21.3@sha256:24a09375a6216764a3eda6a25490a88ac178b5fcb9511d59d0da5ebf9e496474                             0.0s
 => => sha256:d0214956a9c50c300e430c1f6c0a820007ace238e5242c53762e61b344659e05 1.58kB / 1.58kB                                                     0.0s
 => => sha256:015e6b7f599b15be5eecad68608583d19d9fa2c8cc27cb6d254204e080dae199 7.22kB / 7.22kB                                                     0.0s
 => => sha256:0a9573503463fd3166b5b1f34a7720dac28609e98d731e50e7068f92e79b8545 49.58MB / 49.58MB                                                   1.3s
 => => sha256:1ccc26d841b4acc2562483bf393ce1cf8bc358ced09ccd825425226827c79c92 24.05MB / 24.05MB                                                   0.6s
 => => sha256:24a09375a6216764a3eda6a25490a88ac178b5fcb9511d59d0da5ebf9e496474 2.36kB / 2.36kB                                                     0.0s
 => => sha256:800d84653581fc119cd75cd572fa190d3b813d49221b9e5ee463e3560e2cb342 64.13MB / 64.13MB                                                   1.6s
 => => sha256:9211c993294398915ebd0aaf372f7cdb2a3ef81bcbb5af6f71967523546b086d 92.33MB / 92.33MB                                                   2.5s
 => => sha256:b05d0f037378c842afdaeba0cb038a42a0a9b4e29367f1b2537ef5388ed13719 67.00MB / 67.00MB                                                   2.7s
 => => extracting sha256:0a9573503463fd3166b5b1f34a7720dac28609e98d731e50e7068f92e79b8545                                                          3.7s
 => => sha256:9f49ffca687ac5513fd41991a1d5783020961687b147df85d0f43e3c9ff7a084 155B / 155B                                                         1.7s
 => => extracting sha256:1ccc26d841b4acc2562483bf393ce1cf8bc358ced09ccd825425226827c79c92                                                          0.9s
 => => extracting sha256:800d84653581fc119cd75cd572fa190d3b813d49221b9e5ee463e3560e2cb342                                                          4.1s
 => => extracting sha256:9211c993294398915ebd0aaf372f7cdb2a3ef81bcbb5af6f71967523546b086d                                                          4.2s
 => => extracting sha256:b05d0f037378c842afdaeba0cb038a42a0a9b4e29367f1b2537ef5388ed13719                                                          6.6s
 => => extracting sha256:9f49ffca687ac5513fd41991a1d5783020961687b147df85d0f43e3c9ff7a084                                                          0.0s
 => [internal] load build context                                                                                                                  0.0s
 => => transferring context: 503B                                                                                                                  0.0s
 => [2/6] WORKDIR /app                                                                                                                             1.2s
 => [3/6] COPY go.mod ./                                                                                                                           0.1s
 => [4/6] RUN go mod download                                                                                                                      0.4s
 => [5/6] COPY *.go ./                                                                                                                             0.1s
 => [6/6] RUN go build -o /simple-app                                                                                                             25.0s
 => exporting to image                                                                                                                             0.8s
 => => exporting layers                                                                                                                            0.7s
 => => writing image sha256:c451b4bdc00ab0d49a162c760e4c4e0f245834cee525277c8bce17c5268c8f9a                                                       0.0s
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

