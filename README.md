# Ansible k8s Cluster

This repository deploys a Kubernetes cluster with a single master and multiple worker nodes

## Prerequisites

- Ansible 2.14.10
- Rocky Linux 10 or Ubuntu 24.04

## Good to know

- You may specify a `ansible_user` that matches the user on the remote host.
- There are some cluster variables that you can set:
  - `cluster_name` (default: `cluster`)
  - `cluster_domain` (default: `local`)
  - `cluster_service_subnet` (default: `10.96.0.0/12`)
  - `cluster_pod_subnet` (default: `10.244.0.0/16`)
  - `cluster_cni` (default: `flannel`)
  - `cluster_schedule_on_master` (default: `false`)
- Set `kubeadm_skip_kubeproxy` to `true` and set `kube_network_cilium_kubeproxy_replacement` to `true` if you want to use Cilium kube-proxy replacement. Refer to the [Cilium documentation](https://docs.cilium.io/en/latest/network/kubernetes/kubeproxy-free/#kubeproxy-free) for more information.

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

If you will be running the playbook with any encrypted variable, please refer to the [Ansible Vault](#ansible-vault) section.

## Limitations

- Only supports Rocky Linux 10 or Ubuntu 24.04
- Only supports a single master node
- ArgoCD bootstrap process works only `HTTPS` private repository. If you want to use `SSH` repository, it must be public.

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

## Addons

### ArgoCD

ArgoCD is not enabled by default. To enable it, set the `argocd_enabled` variable to `true` in the `inventories/<some name>/hosts.yaml` file.

Refer to the default values in the `roles/argocd/defaults/main.yaml` file for the available options.

As mentioned above, ArgoCD bootstrap works only `HTTPS` private repository. If you want to use `SSH` repository, it must be public.
When using `HTTPS` private repository, you must set `argocd_main_app_repo_private` to `true` and provide `argocd_main_app_repo_username` and `argocd_main_app_repo_password` variables.
For security reasons, it is recommended to use [Ansible Vault](#ansible-vault) to encrypt the password.

### MetalLB

MetalLB is not enabled by default. To enable it, set the `metallb_enabled` variable to `true` in the `inventories/<some name>/hosts.yaml` file.

Refer to the default values in the `roles/metallb/defaults/main.yaml` file for the available options.

## Ansible Vault

You can use [Ansible Vault](https://docs.ansible.com/ansible/latest/user_guide/vault.html) to encrypt sensitive data such as passwords.

### Steps

Create a file somewhere on your machine, for example `~/.ansible-vault-pass.txt`, and add the password to it.

Encrypt a string to use it in a variable:

```bash
ansible-vault encrypt_string --vault-password-file ~/.ansible-vault-pass.txt 'foobar' --name 'argocd_main_app_repo_password'
```

```yaml
Encryption successful
argocd_main_app_repo_password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          65633431303161646637623362306566363836333762346135336534663536333662353364396261
          6465326431663035653035313737613732363533343635350a386563336633346365323065353331
          39646638666235396530383264396633336633623033386338313937303431376536613433666133
          3437323338613930660a373165653239376131623434613566626463303964646362633362366432
          3065
```

If you have 1Password, you may use this command to retrieve the password:

```bash
ansible-vault encrypt_string --vault-password-file ~/.ansible-vault-pass.txt $(op item get 'GitLab Token - dyegoe argocd' --fields token) --name 'argocd_main_app_repo_password'
```

When you run the playbook, you must provide the password file:

```bash
ansible-playbook --vault-password-file ~/.ansible-vault-pass.txt -i inventories/<some name>/hosts.yaml site.yaml
```

## Contributing

Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

### Pre-commit

This repository uses [pre-commit](https://pre-commit.com/) to run some checks before committing the code.

To install it, run the following command:

```bash
pip install pre-commit
```

To install the git hooks, run the following command:

```bash
pre-commit install
```

To mitage an issue with the `ansible-lint` hook, run the following command:

```bash
ansible-galaxy install -r requirements.yaml --force
sudo ansible-galaxy install -r requirements.yaml --force
```
