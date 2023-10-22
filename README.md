# Ansible k8s Cluster

This repository deploys a Kubernetes cluster with a single master and multiple worker nodes

## Prerequisites

- Ansible 2.14.10
- Ubuntu 22.04

## Usage

1. Clone this repository
2. Create a file called `hosts` or `hosts.yaml` under the `inventories/<some name>/` directory.
3. Add the following content to the file, and edit the values as needed:

    ```yaml
    all:
      vars:
        ansible_user: ubuntu
        kube_network_cni: flannel
      children:
        masters:
          hosts:
            master:
              ansible_host: 10.100.200.10
        workers:
          hosts:
            node1:
              ansible_host: 10.100.200.11
            node2:
              ansible_host: 10.100.200.12
    ```

4. Run the following command:

    ```bash
    ansible-playbook -i inventories/<some name>/hosts.yaml site.yaml
    ```

## Limitations

- Only supports Ubuntu 22.04
- Only supports a single master node
- ArgoCD is not HA and the bootstrap process works only `HTTPS` private repository. If you want to use `SSH` repository, it must be public.

## Components

- [ArgoCD](https://argoproj.github.io/argo-cd/) (not enabled by default)
- [Containerd](https://containerd.io/)
- [Helm](https://helm.sh/)
- [Kubernetes](https://kubernetes.io/)
- Network CNI
  - [Flannel](https://github.com/flannel-io/flannel) (default)
  - [Calico](https://www.projectcalico.org/)
  - [Cilium](https://cilium.io/)
- [MetalLB](https://metallb.universe.tf/) (not enabled by default)
- [Traefik](https://traefik.io/traefik/) (not yet implemented)

## Addons

### ArgoCD

ArgoCD is not enabled by default. To enable it, set the `argocd_enabled` variable to `true` in the `inventories/<some name>/group_vars/all.yaml` file.

Refer to the default values in the `roles/argocd/defaults/main.yaml` file for the available options.

### MetalLB

MetalLB is not enabled by default. To enable it, set the `metallb_enabled` variable to `true` in the `inventories/<some name>/group_vars/all.yaml` file.

Refer to the default values in the `roles/metallb/defaults/main.yaml` file for the available options.
