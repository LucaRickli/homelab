# Cilium

## Configuration

<!-- `values.yaml` contains [helm configuration values](). -->

### Environment

- #### Kubernetes cluster endpoint

See `/talos` folder for more info.

```sh
export CLUSTER_ENDPOINT=<endpoint>
```

### Files

- `ipPool.yaml`

  Contains the ip range used to assing external ip's to ingress / loadbalancer services.

- `bgpPeeringPolicy.yaml`

  Contains border gateway protocol peering configuration.

- `l2AnnouncementPolicy.yaml` (Only used if BGP is not.)

  Contains configuration for l2 announcements (ARP). Default interface regex `^enxd83addb6[0-9,a-f]{4}$`

  > To get the interfaces of a talos node run `talosctl get addresses --talosconfig <talosconfig path> -n <node>`.

## Installation

<!-- helm install cilium cilium/cilium --version 1.14.6 -f values.yaml --namespace kube-system -->
<!-- cilium install -f $PWD/values.yaml --version 1.14.6 -->

If you dont want to use BGP you need to replace `--helm-set=bgpControlPlane.enabled=true` with `--helm-set=l2announcements.enabled=true`

```sh
cilium install --version 1.14.6 \
  --helm-set=ipam.mode=kubernetes \
  --helm-set=kubeProxyReplacement=true \
  --helm-set=securityContext.capabilities.ciliumAgent="{CHOWN,KILL,NET_ADMIN,NET_RAW,IPC_LOCK,SYS_ADMIN,SYS_RESOURCE,DAC_OVERRIDE,FOWNER,SETGID,SETUID}" \
  --helm-set=securityContext.capabilities.cleanCiliumState="{NET_ADMIN,SYS_ADMIN,SYS_RESOURCE}" \
  --helm-set=cgroup.autoMount.enabled=false \
  --helm-set=cgroup.hostRoot=/sys/fs/cgroup \
  --helm-set=bgpControlPlane.enabled=true \
  --helm-set=ingressController.enabled=true \
  --helm-set=hubble.enabled=true \
  --helm-set=hubble.ui.enabled=true \
  --helm-set=hubble.relay.enabled=true \
  --helm-set=hubble.peerService.clusterDomain=cluster.home \
  --helm-set=ingressController.loadbalancerMode=dedicated \
  --helm-set=k8sServiceHost=$CLUSTER_ENDPOINT \
  --helm-set=k8sServicePort=6443
```

<!--
  --helm-set=operator.replicas=1 \
-->

### Setup loadbalancer

#### Border gateway protocol

Open a shell in your edgerouter and configure BGP.

```sh
configure
set protocols bgp 64512 parameters router-id 192.168.10.1
set protocols bgp 64512 neighbor 192.168.10.40 remote-as 65000
set protocols bgp 64512 neighbor 192.168.10.40 soft-reconfiguration inbound
set protocols bgp 64512 neighbor 192.168.10.41 remote-as 65000
set protocols bgp 64512 neighbor 192.168.10.41 soft-reconfiguration inbound
set protocols bgp 64512 neighbor 192.168.10.42 remote-as 65000
set protocols bgp 64512 neighbor 192.168.10.42 soft-reconfiguration inbound
set protocols bgp 64512 neighbor 192.168.10.43 remote-as 65000
set protocols bgp 64512 neighbor 192.168.10.43 soft-reconfiguration inbound
set protocols bgp 64512 neighbor 192.168.10.44 remote-as 65000
set protocols bgp 64512 neighbor 192.168.10.44 soft-reconfiguration inbound
set protocols bgp 64512 neighbor 192.168.10.45 remote-as 65000
set protocols bgp 64512 neighbor 192.168.10.45 soft-reconfiguration inbound
commit
save
exit
```

Apply bgp peering policy.

```sh
kubectl apply -f bgpPeeringPolicy.yaml
```

#### Layer 2 announcments

If your network does not support BGP cilium can resolve your services using ARP.

```sh
kubectl apply -f l2AnnouncementPolicy.yaml
```

#### Ip pool

Virtual ip address range used to assign external ip to services. Default `172.16.0.0/16`, change as needed.

```sh
kubectl apply -f ipPool.yaml
```

### Hubble UI

Open the Hubble UI in your browser by running `cilium hubble ui`. It will automatically set up a port forward to the hubble-ui service in your Kubernetes cluster and make it available on a local port on your machine.

If you want to make the UI available for everyone you could expose it with this ingress.

```sh
kubectl apply -f httpIngress.yaml
kubectl apply -f secureIngress.yaml # requires cert-manager
```
