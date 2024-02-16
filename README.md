# Homelab

Local Kubernetes cluster.

## Requirements

- [Kubectl]()
- [Helm]()
- [Talosctl]()
- [Cilium cli]()
<!-- - []() -->

## Setup

- ### [Ubiquiti EdgeRouter X]()

  Acts as a sub-router in my network for homelab use using static routes & proper firewall configuration.

  The IP range has been changed to `192.168.10.0/24`.

- ### [Super6c raspberry pi cluster]()

  6 Raspberry pi CM4 nodes with a single m.2 sdd each connected to an unmanaged switch all on a single board.

  #### Nodes

  | Static IP       | Type         |
  | --------------- | ------------ |
  | `192.168.10.40` | Controlplane |
  | `192.168.10.41` | Controlplane |
  | `192.168.10.42` | Controlplane |
  | `192.168.10.43` | Worker       |
  | `192.168.10.44` | Worker       |
  | `192.168.10.45` | Worker       |
