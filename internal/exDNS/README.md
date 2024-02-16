# External DNS

Resolves ingress hostnames to external ip's.

## Configuration

### Files

- `k8sGateway.yaml`

  Requests the static IP `172.16.1.1` as external DNS endpoint. This IP needs to be inside the CiliumLoadBalancerIPPool range.

## Installation

### Service

```sh
kubectl apply -f k8sGateway.yaml
```

### Clients

You either have to change the DNS server in your router config or in your client config.
