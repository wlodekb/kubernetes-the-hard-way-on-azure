# Kubernetes The Hard Way on Azure

이 자습서는 [Microsoft Azure](https://azure.microsoft.com) 및 [Azure CLI 2.0을](https://github.com/azure/azure-cli) 위해 설계되었습니다. [Google Cloud Platform을](https://github.com/kelseyhightower/kubernetes-the-hard-way) 사용하여 동일한 단계를 설명하는 [Kesley Hightower](https://twitter.com/kelseyhightower)의 훌륭한 [Kubernetes The Hard Way](https://cloud.google.com)의 포크입니다.

Azure 버전은 이 [포크](https://twitter.com/LostInTangent)에서 [Jonathan Carter - @lostintangent](https://github.com/lostintangent/kubernetes-the-hard-way)님의 멋진 결과물을 기반으로합니다. 이 가이드의 Azure 버전 제작에 진정한 기여를 해주신 분입니다.

이 튜토리얼은 쿠버네티스를 어렵게 설정하는 과정을 안내합니다. 이 가이드는 쿠버네티스 클러스터를 시작하기 위해 완전히 자동화 된 명령을 찾는 사람들을 위한 것이 아닙니다. 그 방면으로는 [Azure 컨테이너 서비스 웹 사이트](https://azure.microsoft.com/en-us/services/container-service) 또는 [시작 안내서](http://kubernetes.io/docs/getting-started-guides)를 확인하세요.

Kubernetes The Hard Way는 학습에 최적화되어 있으므로 쿠버네티스 클러스터를 부트 스트랩하는 데 필요한 각 작업을 이해하기 위해 긴 경로를 사용해야합니다.

튜토리얼 마지막에 쿠버네티스 대시보드 만들기 과정이 추가되어 UI를 통해 클러스터를 사용해 볼 수 있습니다.

> 이 학습서의 결과로 만들어진 클러스터는 프로덕션 수준이 아니며, 커뮤니티의 제한적인 지원만을 받을 수 있습니다. 그렇지만 학습을 하는데에는 문제가 없으니 멈추지 마세요!

## 이 가이드의 대상 독자

이 튜토리얼의 독자들은 프로덕션 쿠버네티스 클러스터를 지원할 계획이며 모든 것이 어떻게 결합되는지 이해하고자하는 분들입니다.

## Cluster Details

Kubernetes Hard Way는 구성 요소 간 엔드 투 엔드 암호화와 RBAC 인증을 통해 고 가용성 쿠버네티스 클러스터를 부트 스트랩하는 과정을 안내합니다.

- [Kubernetes](https://github.com/kubernetes/kubernetes) 1.15.0
- [containerd 컨테이너 런타임](https://github.com/containerd/containerd) 1.2.7
- [gVisor](https://github.com/google/gvisor) 최신 버전
- [CNI 컨테이너 네트워킹](https://github.com/containernetworking/cni) 0.7.1
- [etcd](https://github.com/coreos/etcd) v3.3.13
- [CoreDNS](https://github.com/coredns/coredns) v1.5.0

## 실험실

This tutorial assumes you have access to the [Microsoft Azure](https://azure.microsoft.com). While Azure is used for basic infrastructure requirements the lessons learned in this tutorial can be applied to other platforms.

- [Prerequisites](docs/01-prerequisites.md)
- [Installing the Client Tools](docs/02-client-tools.md)
- [Provisioning Compute Resources](docs/03-compute-resources.md)
- [Provisioning the CA and Generating TLS Certificates](docs/04-certificate-authority.md)
- [인증을 위한 쿠버네티스 구성 파일 생성](docs/05-kubernetes-configuration-files.md)
- [Generating the Data Encryption Config and Key](docs/06-data-encryption-keys.md)
- [Bootstrapping the etcd Cluster](docs/07-bootstrapping-etcd.md)
- [쿠버네티스 컨트롤 플레인 부트 스트랩](docs/08-bootstrapping-kubernetes-controllers.md)
- [쿠버네티스 컨트롤 플레인 부트 스트랩](docs/09-bootstrapping-kubernetes-workers.md)
- [원격 액세스를 위한 kubectl 구성](docs/10-configuring-kubectl.md)
- [파드 네트워크 경로 프로비저닝](docs/11-pod-network-routes.md)
- [Deploying the DNS Cluster Add-on](docs/12-dns-addon.md)
- [스모크 테스트](docs/13-smoke-test.md)
- [대시보드](docs/14-dashboard.md)
- [정리하기](docs/15-cleanup.md)
