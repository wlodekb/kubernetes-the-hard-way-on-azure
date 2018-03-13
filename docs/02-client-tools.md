# Installing the Client Tools

In this lab you will install the command line utilities required to complete this tutorial: [cfssl](https://github.com/cloudflare/cfssl), [cfssljson](https://github.com/cloudflare/cfssl), and [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl).

## Install CFSSL

The `cfssl` and `cfssljson` command line utilities will be used to provision a [PKI Infrastructure](https://en.wikipedia.org/wiki/Public_key_infrastructure) and generate TLS certificates.

Download and install `cfssl` and `cfssljson` from the [cfssl repository](https://pkg.cfssl.org):

### OS X

```shell
curl -o cfssl https://pkg.cfssl.org/R1.2/cfssl_darwin-amd64
curl -o cfssljson https://pkg.cfssl.org/R1.2/cfssljson_darwin-amd64
```

```shell
chmod +x cfssl cfssljson
```

```shell
sudo mv cfssl cfssljson /usr/local/bin/
```

### Linux

```shell
wget -q --show-progress --https-only --timestamping \
  https://pkg.cfssl.org/R1.2/cfssl_linux-amd64 \
  https://pkg.cfssl.org/R1.2/cfssljson_linux-amd64
```

```shell
chmod +x cfssl_linux-amd64 cfssljson_linux-amd64
```

```shell
sudo mv cfssl_linux-amd64 /usr/local/bin/cfssl
```

```shell
sudo mv cfssljson_linux-amd64 /usr/local/bin/cfssljson
```
### Windows
Download your version of cfss_windows-386.exe or cfssl_windows-amd64.exe
For windows on 32 bit use powershell, using administrative rights
```shell
PS C:\Windows\system32>Invoke-WebRequest -Uri https://pkg.cfssl.org/R1.2/cfssl_windows-386.exe -OutFile cfssl.exe
PS C:\Windows\system32>Invoke-WebRequest -Uri https://pkg.cfssl.org/R1.2/cfssljson_windows-386.exe -OutFile cfssjson.exe
```
For windows on 64 bit use powershell, using administrative rights
```shell
PS C:\Windows\system32>Invoke-WebRequest -Uri https://pkg.cfssl.org/R1.2/cfssl_windows-amd64.exe -OutFile cfssl.exe
PS C:\Windows\system32>Invoke-WebRequest -Uri https://pkg.cfssl.org/R1.2/mkbundle_windows-amd64.exe -OutFile cfssljson.exe
```


### Verification

Verify `cfssl` version 1.2.0 or higher is installed:

```shell
cfssl version
```

If this step fails with a runtime error, try installing cfssl following instructions on [CloudFlare's repository](https://github.com/cloudflare/cfssl#installation)

> output

```shell
Version: 1.2.0
Revision: dev
Runtime: go1.9
```

```shell
cfssljson -version
```

> output

```shell
Version: 1.2.0
Revision: dev
Runtime: go1.9
```

## Install kubectl

The `kubectl` command line utility is used to interact with the Kubernetes API Server. Download and install `kubectl` from the official release binaries:

### OS X

```shell
curl -o kubectl https://storage.googleapis.com/kubernetes-release/release/v1.9.3/bin/darwin/amd64/kubectl
```

```shell
chmod +x kubectl
```

```shell
sudo mv kubectl /usr/local/bin/
```

### Linux

```shell
wget https://storage.googleapis.com/kubernetes-release/release/v1.9.3/bin/linux/amd64/kubectl
```

```shell
chmod +x kubectl
```

```shell
sudo mv kubectl /usr/local/bin/
```

### Windows
Note you need to have chocolately package manager installed first (https://chocolatey.org/)
```shell
PS C:\Windows\system32>choco install kubernetes-cli
```

### Verification

Verify `kubectl` version 1.9.3 or higher is installed:

```shell
kubectl version --client
```

> output

```shell
Client Version: version.Info{Major:"1", Minor:"9", GitVersion:"v1.9.3", GitCommit:"d2835416544f298c919e2ead3be3d0864b52323b", GitTreeState:"clean", BuildDate:"2018-02-09T21:51:54Z", GoVersion:"go1.9.4", Compiler:"gc", Platform:"darwin/amd64"}
```

Next: [Provisioning Compute Resources](03-compute-resources.md)
