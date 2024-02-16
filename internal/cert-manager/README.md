# Cert manager

Manages certificates for ingress resources.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  annotations:
    kubernetes.io/tls-acme: "true" # enable certificates
```

## Configuration

### Files

- `values.yaml`

Contains [helm configuration values]().

- `issuer.yaml`

Contains default ClusterIssuer used for tls-acme called `external`.

## Installation

```sh
helm repo add jetstack https://charts.jetstack.io
helm install --namespace cert-manager --create-namespace --version v1.13.3 -f values.yaml cert-manager jetstack/cert-manager
```

### Issuer

Create self signed root ca. This creates:

- `ca.key` Private key
- `ca.crt` Public certificate

```sh
openssl genpkey -algorithm RSA -out ca.key
openssl req -x509 -new -key ca.key -out ca.crt -config ca.conf
```

Save as kubernetes secret

```sh
kubectl create secret -n cert-manager tls external-issuer-ca.crt --cert=$PWD/ca.crt --key=$PWD/ca.key
```

Apply issuer

```sh
kubectl apply -f issuer.yaml
```
