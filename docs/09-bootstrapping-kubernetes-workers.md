# Bootstrapping the Kubernetes Worker Nodes

In this lab you will bootstrap three Kubernetes worker nodes. The following components will be installed on each node: [runc](https://github.com/opencontainers/runc), [container networking plugins](https://github.com/containernetworking/cni), [cri-containerd](https://github.com/kubernetes-incubator/cri-containerd), [kubelet](https://kubernetes.io/docs/admin/kubelet), and [kube-proxy](https://kubernetes.io/docs/concepts/cluster-administration/proxies).

## Prerequisites

The commands in this lab must be run on each controller instance: `worker-0`, `worker-1`, and `worker-2`. 
Azure Metadata Instace service cannot be used to set custom property. We have used *tags* on each worker VM to defined POD-CIDR used later.

Retrieve the POD CIDR range for the current compute instance and keep it for later.

```shell
az vm show -g kubernetes --name worker-0 --query "tags" -o tsv
```

> output

```shell
10.200.0.0/24
```

Login to each worker instance using the `az` command to find its public IP and ssh to it. Example:

```shell
WORKER="worker-0"
PUBLIC_IP_ADDRESS=$(az network public-ip show -g kubernetes \
  -n ${WORKER}-pip --query "ipAddress" -otsv)

ssh $(whoami)@${PUBLIC_IP_ADDRESS}
```

## Provisioning a Kubernetes Worker Node

Install the OS dependencies:

```shell
sudo apt-get -y install socat
```

> The socat binary enables support for the `kubectl port-forward` command.

### Download and Install Worker Binaries

```shell
wget -q --show-progress --https-only --timestamping \
  https://github.com/containernetworking/plugins/releases/download/v0.6.0/cni-plugins-amd64-v0.6.0.tgz \
  https://github.com/kubernetes-incubator/cri-containerd/releases/download/v1.0.0-alpha.0/cri-containerd-1.0.0-alpha.0.tar.gz \
  https://storage.googleapis.com/kubernetes-release/release/v1.8.0/bin/linux/amd64/kubectl \
  https://storage.googleapis.com/kubernetes-release/release/v1.8.0/bin/linux/amd64/kube-proxy \
  https://storage.googleapis.com/kubernetes-release/release/v1.8.0/bin/linux/amd64/kubelet
```

Create the installation directories:

```shell
sudo mkdir -p \
  /etc/cni/net.d \
  /opt/cni/bin \
  /var/lib/kubelet \
  /var/lib/kube-proxy \
  /var/lib/kubernetes \
  /var/run/kubernetes
```

Install the worker binaries:

```shell
sudo tar -xvf cni-plugins-amd64-v0.6.0.tgz -C /opt/cni/bin/
```

```shell
sudo tar -xvf cri-containerd-1.0.0-alpha.0.tar.gz -C /
```

```shell
chmod +x kubectl kube-proxy kubelet
```

```shell
sudo mv kubectl kube-proxy kubelet /usr/local/bin/
```

### Configure CNI Networking

Create the `bridge` network configuration file replacing POD_CIDR with address retrieved initially from Azure VM tags:

```shell
POD_CIDR="10.200.0.0/24"
cat > 10-bridge.conf <<EOF
{
    "cniVersion": "0.3.1",
    "name": "bridge",
    "type": "bridge",
    "bridge": "cnio0",
    "isGateway": true,
    "ipMasq": true,
    "ipam": {
        "type": "host-local",
        "ranges": [
          [{"subnet": "${POD_CIDR}"}]
        ],
        "routes": [{"dst": "0.0.0.0/0"}]
    }
}
EOF
```

Create the `loopback` network configuration file:

```shell
cat > 99-loopback.conf <<EOF
{
    "cniVersion": "0.3.1",
    "type": "loopback"
}
EOF
```

Move the network configuration files to the CNI configuration directory:

```shell
sudo mv 10-bridge.conf 99-loopback.conf /etc/cni/net.d/
```

### Configure the Kubelet

```shell
sudo mv ${HOSTNAME}-key.pem ${HOSTNAME}.pem /var/lib/kubelet/
```

```shell
sudo mv ${HOSTNAME}.kubeconfig /var/lib/kubelet/kubeconfig
```

```shell
sudo mv ca.pem /var/lib/kubernetes/
```

Create the `kubelet.service` systemd unit file:

```shell
cat > kubelet.service <<EOF
[Unit]
Description=Kubernetes Kubelet
Documentation=https://github.com/GoogleCloudPlatform/kubernetes
After=cri-containerd.service
Requires=cri-containerd.service

[Service]
ExecStart=/usr/local/bin/kubelet \\
  --allow-privileged=true \\
  --anonymous-auth=false \\
  --authorization-mode=Webhook \\
  --client-ca-file=/var/lib/kubernetes/ca.pem \\
  --cluster-dns=10.32.0.10 \\
  --cluster-domain=cluster.local \\
  --container-runtime=remote \\
  --container-runtime-endpoint=unix:///var/run/cri-containerd.sock \\
  --image-pull-progress-deadline=2m \\
  --kubeconfig=/var/lib/kubelet/kubeconfig \\
  --network-plugin=cni \\
  --pod-cidr=${POD_CIDR} \\
  --register-node=true \\
  --require-kubeconfig \\
  --runtime-request-timeout=15m \\
  --tls-cert-file=/var/lib/kubelet/${HOSTNAME}.pem \\
  --tls-private-key-file=/var/lib/kubelet/${HOSTNAME}-key.pem \\
  --v=2
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
EOF
```

### Configure the Kubernetes Proxy

```shell
sudo mv kube-proxy.kubeconfig /var/lib/kube-proxy/kubeconfig
```

Create the `kube-proxy.service` systemd unit file:

```shell
cat > kube-proxy.service <<EOF
[Unit]
Description=Kubernetes Kube Proxy
Documentation=https://github.com/GoogleCloudPlatform/kubernetes

[Service]
ExecStart=/usr/local/bin/kube-proxy \\
  --cluster-cidr=10.200.0.0/16 \\
  --kubeconfig=/var/lib/kube-proxy/kubeconfig \\
  --proxy-mode=iptables \\
  --v=2
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
EOF
```

### Start the Worker Services

```shell
sudo mv kubelet.service kube-proxy.service /etc/systemd/system/
```

```shell
sudo systemctl daemon-reload
```

```shell
sudo systemctl enable containerd cri-containerd kubelet kube-proxy
```

```shell
sudo systemctl start containerd cri-containerd kubelet kube-proxy
```

> Remember to run the above commands on each worker node: `worker-0`, `worker-1`, and `worker-2`.

## Verification

Login to one of the controller nodes:

```shell
CONTROLLER="controller-0"
PUBLIC_IP_ADDRESS=$(az network public-ip show -g kubernetes \
  -n ${CONTROLLER}-pip --query "ipAddress" -otsv)

ssh $(whoami)@${PUBLIC_IP_ADDRESS}
```

List the registered Kubernetes nodes:

```shell
kubectl get nodes
```

> output

```shell
NAME       STATUS    AGE       VERSION
worker-0   Ready     5m        v1.8.0
worker-1   Ready     3m        v1.8.0
worker-2   Ready     7s        v1.8.0
```

Next: [Configuring kubectl for Remote Access](10-configuring-kubectl.md)
