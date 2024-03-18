# Cilium

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: example
spec:
  ingressClassName: cilium # let cilium handle ingress
  ...
```

## Configuration

### Files

- `values.yaml`
  contains [helm configuration values](https://docs.cilium.io/en/stable/helm-reference/).

### Environment

- #### Kubernetes cluster endpoint

The kubernetes api server endpoint. If you are using kubeprism set it to localhost on port 7445.

```sh
export CLUSTER_ENDPOINT=<endpoint>
```

### Files

- `ipPool.yaml`

Contains the ip range used to assign external ip's to ingress / loadbalancer services.

- `bgpPeeringPolicy.yaml`

Contains border gateway protocol peering configuration.

- `l2AnnouncementPolicy.yaml` (Only used if BGP is not.)

Contains configuration for l2 announcements (ARP). Default interface regex `^enxd83addb6[0-9,a-f]{4}$`

> To get the interfaces of a talos node run `talosctl get addresses --talosconfig <talosconfig path> -n <node>`.

## Installation

### Helm

```sh
helm install cilium --version 1.14.6 -f values.yaml --namespace kube-system cilium/cilium
```

### Cilium cli

If you don't want to use BGP you need to replace `--helm-set=bgpControlPlane.enabled=true` with `--helm-set=l2announcements.enabled=true`

```sh
cilium install --version 1.14.6 \
  --helm-set=ipam.mode=kubernetes \
  --helm-set=kubeProxyReplacement=true \
  --helm-set=securityContext.capabilities.ciliumAgent="{CHOWN,KILL,NET_ADMIN,NET_RAW,IPC_LOCK,SYS_ADMIN,SYS_RESOURCE,DAC_OVERRIDE,FOWNER,SETGID,SETUID}" \
  --helm-set=securityContext.capabilities.cleanCiliumState="{NET_ADMIN,SYS_ADMIN,SYS_RESOURCE}" \
  --helm-set=cgroup.autoMount.enabled=false \
  --helm-set=cgroup.hostRoot=/sys/fs/cgroup \
  --helm-set=ingressController.enabled=true \
  --helm-set=ingressController.loadbalancerMode=dedicated \
  --helm-set=bgpControlPlane.enabled=true \
  --helm-set=hubble.enabled=true \
  --helm-set=hubble.ui.enabled=true \
  --helm-set=hubble.relay.enabled=true \
  --helm-set=hubble.peerService.clusterDomain=cluster.home \
  --helm-set=hubble.metrics.enableOpenMetrics=true \
  --helm-set=prometheus.enabled=true \
  --helm-set=operator.prometheus.enabled=true \
  --helm-set=k8sServiceHost=localhost \
  --helm-set=k8sServicePort=7445
```

> If you run a single node cluster you can scale down the operators with `--helm-set=operator.replicas=1`.

### Setup loadbalancer

#### Border gateway protocol

Open a shell in your edgerouter and configure BGP. Set each node as neighbor and allow inbound soft-reconfiguration.

```sh
configure
set protocols bgp 64512 parameters router-id 192.168.10.1
set protocols bgp 64512 neighbor <node-ip> remote-as 65000
set protocols bgp 64512 neighbor <node-ip> soft-reconfiguration inbound
commit
save
exit
```

Apply bgp peering policy.

```sh
kubectl apply -f bgpPeeringPolicy.yaml
```

#### Layer 2 announcements

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
kubectl apply -f secureIngress.yaml # requires cert-manager (change issuer)
```

## Debugging

### Cilium

Show the status of the cilium deployments.

```sh
cilium status --wait
```

### BPG

Show BGP routes on the edgerouter.

```sh
show ip bgp
```
