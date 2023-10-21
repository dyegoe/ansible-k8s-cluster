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
        argocd_enabled: true
        metallb_enabled: true
        metallb_ip_pool: "10.100.200.80-10.100.200.89"
        metallb_interface: "ens3"
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
