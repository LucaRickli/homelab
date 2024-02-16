# Talos

Create a multi node kubernetes cluster using [Talos os](https://www.talos.dev/).

## Configuration

### Environment

- #### Kubernetes cluster endpoint

If you desire multiple controlplane nodes the cluster endpoint needs to be a dns record pointing to your nodes.

```sh
export CLUSTER_ENDPOINT=<endpoint>
```

- #### Install disk

List all disks and choose the one you want to install the os onto. Do this for every node and note down the disks.

```sh
talosctl disks --insecure -n <ip>
export INSTALL_DISK=<disk>
```

### Generate config

This contains a patch allowing containers to be scheduled on controlplane(s) and some configuration for cilium.

```sh
talosctl gen config my-cluster https://$CLUSTER_ENDPOINT:6443 \
  --install-disk $INSTALL_DISK \
  --config-patch @patch.yaml
```

This should create:

- controlplane.yaml
- worker.yaml
- talosconfig

Replace `context.my-cluster.endpoints` inside `talosconfig` with all controlplane endpoints of your cluster.

## Install

### Apply config

Use the `-n` flag to target specific node.

> If you have multiple nodes and the install disks dont match you have to edit `machine.install.disk` in the yaml configuration for each node before applying.

#### Controlplane

```sh
talosctl apply-config --file controlplane.yaml --insecure -n <ip>
```

#### Worker

```sh
talosctl apply-config --file worker.yaml --insecure -n <ip>
```

### Bootstrap nodes

Once you configured your nodes you can bootstrap the cluster. Choose a single controlplane that starts the bootstrapping process.

```sh
talosctl bootstrap --talosconfig $PWD/talosconfig -n <ip>
```

### Grab kubeconfig

You can grab a kubeconfigs anytime as long as you have the `talosconfig` file.

```sh
talosctl kubeconfig --talosconfig $PWD/talosconfig -n <ip>
```

### Dashboard

View the usage and logs of a node.

```sh
talosctl dashboard --talosconfig $PWD/talosconfig -n <ip>
```

> If you desire a web dashboard you could use [thelia](https://github.com/siderolabs/theila). But be aware that this project has been archived and is no longer mantained.
