# Sidero Omni

Kubernetes cluster management.

## Configuration

### Certificate

Omni requires a valid trusted (letsencrypt/zerossl/buypass/...) certificate to work properly.

<!-- - `example.env`

Contains environment configuration values. -->

- `chain.crt` Your TLS public certificate chain.

- `tls.key` Your TLS private key.

### Etcd encryption key

Generate a GPG key (without passphrase).

```sh
gpg --quick-generate-key "Omni (Used for etcd data encryption) info@example.com" rsa4096 cert never
```

Find the fingerprint of the generated key.

```sh
gpg --list-secret-keys
```

Using the fingerprint, add an encryption subkey and export.

```sh
gpg --quick-add-key <fingerprint> rsa4096 encr never
gpg --export-secret-key --armor info@example.com > omni.asc
```

### Omni account id

This gets used to identify your instance.

```sh
uuidgen | tee omni.uuid
export OMNI_ACCOUNT_UUID=$(cat omni.uuid)
```

## Installation

<!-- Configure your environment.

```sh
cp example.env .env && vim .env
``` -->

If you plan on running omni on arm64 (raspberry pi) you have to build your own image.

```sh
docker build . -t ghcr.io/siderolabs/omni:v0.31.1
```

Run the omni docker container.

<!-- ```sh
docker compose up -d
``` -->

```sh
export OMNI_HOSTNAME=<host>

docker run -d --name omni \
  --net=host \
  --cap-add=NET_ADMIN \
  -v $PWD/omni.asc:/omni.asc \
  -v $PWD/chain.crt:/tls.crt \
  -v $PWD/tls.key:/tls.key \
  -v $PWD/etcd:/_out/etcd \
  -v /var/run/docker.sock:/var/run/docker.sock \
  ghcr.io/siderolabs/omni:v0.31.1 \
    --account-id=$(cat $PWD/omni.uuid) \
    --name=onprem-omni \
    --cert=/tls.crt \
    --key=/tls.key \
    --siderolink-api-cert=/tls.crt \
    --siderolink-api-key=/tls.key \
    --private-key-source=file:///omni.asc \
    --event-sink-port=8091 \
    --bind-addr=0.0.0.0:443 \
    --siderolink-api-bind-addr=0.0.0.0:8090 \
    --k8s-proxy-bind-addr=0.0.0.0:8100 \
    --advertised-api-url=https://${OMNI_HOSTNAME:?required}/ \
    --siderolink-api-advertised-url=https://${OMNI_HOSTNAME:?required}:8090/ \
    --advertised-kubernetes-proxy-url=https://${OMNI_HOSTNAME:?required}:8100/ \
    --auth-auth0-enabled=true \
    --auth-auth0-domain=<Auth0 domain> \
    --auth-auth0-client-id=<Auth0 client ID> \
    --initial-users=<email address> \
    --siderolink-wireguard-advertised-addr=<ip>:50180
```

<!-- ```sh
OMNI_ACCOUNT_UUID=$(cat $PWD/omni.uuid) docker compose up -d
``` -->

## Create cluster

You can login with your identity provider on the configured `OMNI_HOSTNAME` url. From there you can create, update or delete your cluster(s) from the ui. If you want to administer your cluster you can download a talosconfig or kubeconfig from the cluster overview.

> [!NOTE]  
> Kubectl requires the [kubelogin plugin](https://github.com/int128/kubelogin) to authenticate to the cluster via oauth (omni kube proxy).

> If you want to use cilium as kube-proxy replacement you should include the `patch.yaml` during or after creating the cluster.
