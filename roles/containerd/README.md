# Containerd Role

This Ansible role installs and configures Containerd, a high-performance container runtime.

## List of commands for managing Containerd

```bash
# List namespaces
ctr namespaces list

# List all containers
ctr containers list

# List container for a specific namespace
ctr -n <namespace> containers list

# List all images
ctr images list
```
