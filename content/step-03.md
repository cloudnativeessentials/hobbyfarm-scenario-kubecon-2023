+++
title = "Containerize an Application: Creating simple-app"
weight = 3
+++

## Create a simple golang application

1. Since we'll test a simple golang application, install golang

```ctr:harbor
curl -OL https://golang.org/dl/go1.21.3.linux-amd64.tar.gz
```

2. Verify the sha256 checksum
The sha256 checksum for `go1.21.3.linux-amd64.tar.gz` is `1241381b2843fae5a9707eec1f8fb2ef94d827990582c7c7c32f5bdfbfd420c8` which can be found at [https://go.dev/dl](https://go.dev/dl).
Verify the sha256 checksum.

```ctr:harbor
sha256sum go1.21.3.linux-amd64.tar.gz
echo "1241381b2843fae5a9707eec1f8fb2ef94d827990582c7c7c32f5bdfbfd420c8 go1.21.3.linux-amd64.tar.gz" | sha256sum --check
```

Expected output:

```shell
go1.21.3.linux-amd64.tar.gz: OK
```

3. Extract the tarball to `/usr/local`

```ctr:harbor
sudo tar -C /usr/local -xvf go1.21.3.linux-amd64.tar.gz
```

4. Add go's path to the `.profile`

```ctr:harbor
sed -i -e '$aexport PATH=$PATH:/usr/local/go/bin' ~/.profile
source ~/.profile
```

5. Create the simple golang application
Create the directory for the golang application

```ctr:harbor
mkdir -p ~/simple-app
```

Create the simple golang application source

```ctr:harbor
cat <<EOF > ~/simple-app/main.go
package main

import (
	"fmt"
	"net/http"
	"os"
	"log"
)

func main() {
	logger := log.New(os.Stdout, "http: ", log.LstdFlags)
	logger.Printf("Server is starting...")
	http.HandleFunc("/", handler)
	http.ListenAndServe(":8080", nil)
}

func handler(w http.ResponseWriter, r *http.Request) {
	hostname, err := os.Hostname()
	if err != nil {
		fmt.Println(err)
		os.Exit(1)
	}
	fmt.Fprintf(w, "Hostname is " + hostname)
}

EOF
```

6. Test the application

```ctr:harbor
go run ~/simple-app/main.go
```

When you see

```shell
http: 2023/MM/DD HH:MM:SS Server is starting...
```
7. Open a browser to test the application
<a href="http://harbor.${vminfo:harbor:public_ip}.sslip.io:8080" target="_blank">http://harbor.${vminfo:harbor:public_ip}.sslip.io:8080</a>

8. Stop the program from running with ctrl+c


