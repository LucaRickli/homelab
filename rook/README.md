# Rook

There are [some considerations regarding talos linux](https://www.talos.dev/v1.6/kubernetes-guides/configuration/ceph-with-rook/#talos-linux-considerations) to keep in mind.

> Requires 3+ nodes with each having 1+ disk without a filesystem for ceph to use.

## Configuration

### Files

- `ceph-values.yaml`

Contains [helm configuration values]() for the rook-ceph install.

## Installation

### Helm repo

```sh
helm repo add rook-release https://charts.rook.io/release
```

### Namespace

Due to rook & ceph needing access to privileged cluster & system resources you have to create a namespace that allows this.

```sh
kubectl apply -f namespace.yaml
```

### Operator

```sh
helm install --version v1.13.7 --namespace rook-ceph rook-ceph rook-release/rook-ceph
```

### Ceph cluster

```sh
helm install --version v1.13.7 --namespace rook-ceph rook-ceph-cluster -f $PWD/ceph-values.yaml rook-release/rook-ceph-cluster
```

### Ceph toolbox

```sh
kubectl apply -f toolbox.yaml
```

You can use this container do administer you ceph cluster using the ceph cli.

```sh
kubectl exec --stdin --tty -n rook-ceph rook-ceph-tools-... -- /bin/bash
```

### Ceph dashboard

Due to [a bug](https://github.com/rook/rook/issues/9182) in some versions of rook you have to set the config in the toolbox manually.

```sh
ceph config set mgr mgr/dashboard/ssl false
ceph config set mgr mgr/dashboard/server_port 7000
```

#### Admin password

login: `admin`

Password is stored as a kubernetes secret

```sh
kubectl -n rook-ceph get secret rook-ceph-dashboard-password -o jsonpath="{['data']['password']}" | base64 --decode && echo
```
