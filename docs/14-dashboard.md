# Dashboard configuration

In this lab you will install configure Kubernetes dashboard.

## Installation

Run the following command to deploy the dashboard:

```shell
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard.yaml
```

Create a proxy to the dashboard:

```shell
kubectl proxy
```

To access the dashboard in your web browser, click the following link: http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/

> output
A login page asking for kubeconfig file or a token

A service account and its credentials are needed to login.

## Service Account creation

Create the service account with following command:

```shell
kubectl create serviceaccount dashboard -n default
```

Next, add the cluster binding rules to it with this command:

```shell
kubectl create clusterrolebinding dashboard-admin -n default --clusterrole=cluster-admin --serviceaccount=default:dashboard
```

Copy the secret token required for your dashboard login using the below command:

```shell
kubectl get secret $(kubectl get serviceaccount dashboard -o jsonpath="{.secrets[0].name}") -o jsonpath="{.data.token}" | base64 --decode
```

Finally, just copy the secret token and paste it in Dashboard Login interface, by selecting the token option (second radiobox). After Sign In you will be redirected to the Kubernetes Dashboard Homepage.

Next: [Cleaning Up](15-cleanup.md)
