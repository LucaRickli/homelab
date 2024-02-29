# Homelab

Local Kubernetes cluster.

## Requirements

- [Cilium cli](https://docs.cilium.io/en/stable/gettingstarted/k8s-install-default/#install-the-cilium-cli)
- [CMctl](https://cert-manager.io/docs/reference/cmctl/#installation) (optional)
- [Helm](https://helm.sh/docs/helm/helm_install/)
- [Kubectl](https://kubernetes.io/docs/tasks/tools/#kubectl)
- [Talosctl](https://www.talos.dev/v1.6/introduction/getting-started/#talosctl)
<!-- - []() -->

## Setup

- ### [Ubiquiti EdgeRouter X]()

  Acts as a sub-router in my network using static routes & proper firewall configuration.

- ### [Super6c raspberry pi cluster]()

  6 raspberry pi CM4 nodes with a single m.2 sdd each connected to an unmanaged switch all on a single board.

## Stack

| Name                                                                                  | Use                                                     | Default endpoint(s)                                    |
| ------------------------------------------------------------------------------------- | ------------------------------------------------------- | ------------------------------------------------------ |
| [Cert manager](https://github.com/LucaRickli/homelab/tree/main/internal/cert-manager) | Certificates for ingress resources                      | kube-apiserver                                         |
| [Cilium](https://github.com/LucaRickli/homelab/tree/main/internal/cilium)             | Networking, loadbalancer & security                     | [hubble.services.home](https://hubble.services.home)   |
| [ExDNS](https://github.com/LucaRickli/homelab/tree/main/internal/exDNS)               | Resolves ingress hostnames to corresponding external ip | 172.16.1.1/UDP                                         |
| [Netdata](https://github.com/LucaRickli/homelab/tree/main/internal/netdata)           | Monitoring & alerting                                   | [netdata.services.home](https://netdata.services.home) |
| [Talos linux](https://github.com/LucaRickli/homelab/tree/main/talos)                  | Operating system designed to run kubernetes             | see ip ranges                                          |

<!-- | []()             |                                                         | -->

## IP ranges

### EdgeRouter

`192.168.10.0/24`

### Kubernetes nodes

| Static IP       | Role         | Type                 |
| --------------- | ------------ | -------------------- |
| `192.168.10.40` | Controlplane | Raspberry pi CM4 8GB |
| `192.168.10.41` | Controlplane | Raspberry pi CM4 8GB |
| `192.168.10.42` | Controlplane | Raspberry pi CM4 8GB |
| `192.168.10.43` | Worker       | Raspberry pi CM4 8GB |
| `192.168.10.44` | Worker       | Raspberry pi CM4 8GB |
| `192.168.10.45` | Worker       | Raspberry pi CM4 8GB |
