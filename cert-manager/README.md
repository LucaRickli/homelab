# Cert manager

Manages certificates for ingress resources.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  annotations:
    # Enable certificate issuer with metadata.name: default.
    kubernetes.io/tls-acme: "true"
    # Or add an annotation indicating the issuer to use.
    cert-manager.io/cluster-issuer: nameOfClusterIssuer
```

## Configuration

### Files

- `values.yaml`

Contains [helm configuration values]().

### Issuers

All issuers are located inside `issuer/`.

#### Private

Theses issuers are not trusted by your clients by default.

- `ca.yaml`

#### Public

Get valid (wildcard) certificates trusted by most clients.

- `cloudflare-letsencrypt-staging.yaml`
- `cloudflare-letsencrypt.yaml`
- `cloudflare-buypass.yaml`

## Installation

### Helm repo

```sh
helm repo add jetstack https://charts.jetstack.io
```

### Helm chart

```sh
helm install --namespace cert-manager --create-namespace --version v1.13.3 -f $PWD/values.yaml cert-manager jetstack/cert-manager
```

### Issuer(s)

#### Private CA

Create a private root CA key & certificate.

```sh
openssl genpkey -algorithm RSA -out ca.key
openssl req -x509 -new -key ca.key -out ca.crt -config ca.conf
```

This creates:

- `ca.key` Private key
- `ca.crt` Public certificate

> Install ca.crt onto your clients to trust any certificates signed by your root CA.

Save the created key & certificate as a kubernetes tls secret.

```sh
kubectl create secret -n cert-manager tls ca-issuer-ca.crt --cert=$PWD/ca.crt --key=$PWD/ca.key
```

Apply issuer.

```sh
kubectl apply -f $PWD/issuers/ca.yaml
```

#### Cloudflare

In order to use this issuer you have to store a Cloudflare API token as kubernetes secret. See [the cert-manager documentation](https://cert-manager.io/docs/configuration/acme/dns01/cloudflare/#api-tokens) on how to create tokens.

```sh
kubectl create secret -n cert-manager generic cloudflare-dns --from-literal=api-token=<token>
```

Apply issuer.

```sh
kubectl apply -f $PWD/issuers/cloudflare-<ca>.yaml
```
