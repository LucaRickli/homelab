# Netdata

## Configuration

### Files

- `values.yaml`

Contains [helm configuration values](https://learn.netdata.cloud/docs/installing/kubernetes-helm-chart-reference).

## Installation

```sh
kubectl apply -f namespace.yaml
```

```sh
helm repo add netdata https://netdata.github.io/helmchart/
helm install --namespace netdata -f $PWD/values.yaml netdata netdata/netdata
```
