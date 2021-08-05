## Install Kube-Prometheus-Stack

Steps to install prometheus operator along with grafana

## Prerequisites

- [Helm](https://helm.sh/docs/intro/install/)

## Set Up Commands

```yaml
kubectl create ns monitoring
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install prom prometheus-community/kube-prometheus-stack --namespace monitoring
```

