# Sidero Omni

Kubernetes cluster management.

## Depends on

- Traefik

## Configuration

> [!NOTE]  
> Omni requires a valid trusted (letsencrypt/zerossl/buypass/...) certificate to work properly.  
> **A self-signed certificate will not work!**

<!-- ### Files - `example.env` Example environment configuration. -->

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

### Environment configuration

```sh
cp example.env .env
echo "OMNI_ACCOUNT_UUID=$(uuidgen)" >> .env
vim .env
```

> If uuidgen is no recognized as a command try installing the package `uuid-runtime`.

## Installation

> If you plan on running omni on arm64 (raspberry pi) you have to build your own image.
>
> ```sh
> docker build . -t TAG:VERSION
> ```

Run the omni docker container.

```sh
docker compose up -d
```

## Create cluster

You can login with your identity provider on the configured `OMNI_HOSTNAME` url.
From there you can create or modify your clusters and download a talosconfig or kubeconfig.

> [!NOTE]  
> Kubectl requires the [kubelogin plugin](https://github.com/int128/kubelogin) to authenticate to the cluster via oauth (omni kube proxy).
