# Ansible k8s Cluster

This repository deploys a Kubernetes cluster with a single master and multiple worker nodes

## Prerequisites

- Ansible 2.14.10
- Ubuntu 22.04

## Usage

1. Clone this repository
2. Create a file called `hosts` or `hosts.yaml` under the `inventories/<some name>/` directory.
3. Add the following content to the file:

    ```yaml
    all:
      vars:
        ansible_user: ubuntu
      children:
        masters:
          hosts:
            master:
              ansible_host: 10.100.200.2
        nodes:
          hosts:
            node01:
              ansible_host: 10.100.200.3
            node02:
              ansible_host: 10.100.200.4
    ```

4. Run the following command:

    ```bash
    ansible-playbook -i inventories/<some name>/hosts.yaml site.yaml
    ```

## Limitations

- Only supports Ubuntu 22.04
- Only supports a single master node
