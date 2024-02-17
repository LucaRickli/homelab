# Homelab

Local Kubernetes cluster.

## Requirements

- [Cilium cli]()
- [CMctl](https://cert-manager.io/docs/reference/cmctl/#installation)
- [Helm]()
- [Kubectl]()
- [Talosctl]()
<!-- - []() -->

## Setup

- ### [Ubiquiti EdgeRouter X]()

  Acts as a sub-router in my network using static routes & proper firewall configuration.

- ### [Super6c raspberry pi cluster]()

  6 raspberry pi CM4 nodes with a single m.2 sdd each connected to an unmanaged switch all on a single board.

## Stack

| Name             | Use                                                     |
| ---------------- | ------------------------------------------------------- |
| [Cert manager]() | Certificates for ingress resources                      |
| [Cilium]()       | Networking, loadbalancer & security                     |
| [ExDNS]()        | Resolves ingress hostnames to corresponding external ip |
| [Netdata]()      | Monitoring & alerting                                   |
| [Talos linux]()  | Operating system designed to run kubernetes             |

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
