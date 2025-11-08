# Kube Network module

## Cilium

### Sample App

```bash
kubectl create -f https://raw.githubusercontent.com/cilium/cilium/refs/tags/v1.18.3/examples/minikube/http-sw-app.yaml
```

#### Test

```bash
kubectl exec xwing -- curl -s -XPOST deathstar.default.svc.k8s.local/v1/request-landing
```

Output: `Ship landed`

```bash
kubectl exec tiefighter -- curl -s -XPOST deathstar.default.svc.k8s.local/v1/request-landing
```

Output: `Ship landed`

#### Create a NetworkPolicy

```yaml
apiVersion: "cilium.io/v2"
kind: CiliumNetworkPolicy
metadata:
  name: allow-empire-deathstar
spec:
  description: "L3-L4 policy to restrict deathstar access to empire ships only"
  endpointSelector:
    matchLabels:
      org: empire
      class: deathstar
  ingress:
    - fromEndpoints:
        - matchLabels:
            org: empire
      toPorts:
        - ports:
            - port: "80"
              protocol: TCP
```

#### Test again

```bash
kubectl exec xwing -- curl -s -XPOST deathstar.default.svc.k8s.local/v1/request-landing
```

Output: `command terminated with exit code 7`

```bash
kubectl exec tiefighter -- curl -s -XPOST deathstar.default.svc.k8s.local/v1/request-landing
```

Output: `Ship landed`

### Cilium Hubble

To enable Hubbble UI, add the following to your inventory or group_vars:

```yaml
---
all:
  vars:
...
    kube_network_cilium_hubble_enabled: true
    kube_network_cilium_hubble_relay_enabled: true
    kube_network_cilium_hubble_ui_enabled: true
```

Launch the Hubble UI:

```bash
cilium hubble ui
```
