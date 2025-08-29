# Ansible Server Configuration Collection

This repository contains a collection of Ansible playbooks and roles for automating the setup and configuration of servers in various environments.

## Overview

You'll find playbooks here for these main use cases:

- Creating and configuring LXC containers on Proxmox
- Deploying and configuring servers on Hetzner Cloud
- Configuring existing Debian-based servers by IP or hostname
- Updating base configuration across multiple hosts

All playbooks are built on a shared set of roles that cover different aspects of server configuration.

## Requirements

- Ansible 2.12 or newer
- Python 3.9 or newer
- Tailscale for dynamic inventory management
- 1Password CLI for secrets management

## Playbooks

### [`create_debian_lxc_pve.yaml`](../create_debian_lxc_pve.yaml)
Create and configure a Debian LXC container in a Proxmox environment.

### [`create_hetzner.yaml`](../create_hetzner.yaml)
Create and configure a new server on Hetzner Cloud.

### [`setup_generic_debian_by_ip.yaml`](../setup_generic_debian_by_ip.yaml)
Configure an existing Debian-based server by its IP address or hostname.

### [`update_inventory.yaml`](../update_inventory.yaml)
Update the base configuration on all hosts in your Tailscale network.

## Roles

This repo includes the following roles:

- `base-system`: Basic system configuration
- `security`: Server security settings
- `shell-config`: ZSH setup with Oh-My-Zsh
- `monitoring-tools`: Monitoring tools for servers
- `backup-tools`: Backup configuration with Backrest
- `container-tools`: Docker and container management
- `pangolin-newt`: Integration with Pangolin API and Newt
- `proxmox-setup`: Proxmox-specific configuration

## Inventory

The repository uses a dynamic Tailscale inventory (`inventories/tailscale.py`) that automatically discovers and groups all hosts available in your Tailscale network.

## Usage

1. Clone this repository
2. Install dependencies:
   ```
   ansible-galaxy collection install -r requirements.yml
   ```
3. Run a playbook:
   ```
   ansible-playbook create_debian_lxc_pve.yaml
   ```

## Security Note

These playbooks use 1Password for secrets management. Make sure you have the 1Password CLI installed and configured before running any playbooks.

## License

MIT