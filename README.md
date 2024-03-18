# Homelab

On-prem Kubernetes cluster.

## Requirements

### Command line tools

#### Required

- [Helm](https://helm.sh/docs/helm/helm_install/)
- [Kubectl](https://kubernetes.io/docs/tasks/tools/#kubectl)
- [Kubelogin](https://github.com/int128/kubelogin)

#### Optional

- [Cilium cli](https://docs.cilium.io/en/stable/gettingstarted/k8s-install-default/#install-the-cilium-cli)
- [CMctl](https://cert-manager.io/docs/reference/cmctl/#installation)
- [Talosctl](https://www.talos.dev/v1.6/introduction/getting-started/#talosctl)

<!-- - []() -->

## Hardware

- ### [Ubiquiti EdgeRouter X]()

  Acts as a sub-router in my network using static routes & proper firewall configuration.

- ### [Super6c raspberry pi cluster](https://deskpi.com/products/deskpi-super6c-raspberry-pi-cm4-cluster-mini-itx-board-6-rpi-cm4-supported)

  6 raspberry pi CM4 nodes with a single m.2 sdd each connected to an unmanaged switch all on a single board.

- ### [Raspberry pi 4 4GB]()

  Runs some docker services that cant run inside the cluster.

## Setup

### Kubernetes

> Keep in mind that this stuff cant run inside the kubernetes cluster and requires a separate docker host.

<!-- - #### [Harbor](https://github.com/LucaRickli/homelab/tree/main/omni)

  > [!NOTE]
  > Due to platform limitations (amd64 only) this is currently not tested or fully implemented.

  - Stores & scans all container images. -->

- #### [Sidero omni](https://github.com/LucaRickli/homelab/tree/main/omni)

  > Alternatively you could just use [Talos linux](https://github.com/LucaRickli/homelab/tree/main/talos) and [talosctl](https://www.talos.dev/v1.6/introduction/getting-started/#talosctl) to manage your nodes.

  - Custom pre configured Talos linux images.
  - Cluster lifecycle management.
  - Kubernetes API access control (OAuth).

### Networking

- #### [Cilium](https://github.com/LucaRickli/homelab/tree/main/cilium)

  - Replaces kube-proxy.
  - Acts as LoadBalancer & assigns external ip's.
  - Networking policies.
  - Service map (hubble).

- #### [External DNS](https://github.com/LucaRickli/homelab/tree/main/exDNS)

  > Endpoint: `172.16.1.1`

  - Resolves ingress hostnames to corresponding external ip.

### Certificates

- #### [Cert manager](https://github.com/LucaRickli/homelab/tree/main/cert-manager)

  - Manages tls certificates for ingress resources thru private or public key infrastructure.

### Storage

- #### [Rook ceph](https://github.com/LucaRickli/homelab/tree/main/rook)

  - Provides storage for the kubernetes cluster.

### Monitoring

- #### [Netdata](https://github.com/LucaRickli/homelab/tree/main/netdata)

  - Monitors each node and provides a dashboard without a big configuration mess.

## Services

| Name                                                                     | Use                          | Endpoint(s)                                          | Platform   |
| ------------------------------------------------------------------------ | ---------------------------- | ---------------------------------------------------- | ---------- |
| [Ceph dashboard](https://github.com/LucaRickli/homelab/tree/main/cilium) | Storage administration       | [ceph.rickli.cloud](https://ceph.rickli.cloud)       | Kubernetes |
| [Cilium hubble](https://github.com/LucaRickli/homelab/tree/main/cilium)  | Service map                  | [hubble.rickli.cloud](https://hubble.rickli.cloud)   | Kubernetes |
| [Netdata](https://github.com/LucaRickli/homelab/tree/main/netdata)       | Monitoring & alerting        | [netdata.rickli.cloud](https://netdata.rickli.cloud) | Kubernetes |
| [Sidero omni](https://github.com/LucaRickli/homelab/tree/main/)          | Cluster lifecycle management | [omni.rickli.cloud](https://omni.rickli.cloud)       | Docker     |

<!-- | [](https://github.com/LucaRickli/homelab/tree/main/)                    |                       | [<url>](https://<url>)                               | Kubernetes | -->

## IP ranges

### EdgeRouter

IP range: `192.168.10.0/24`

### kubernetes services (virtual)

IP range: `172.16.0.0/16`
