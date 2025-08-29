# Ansible Infrastructure Scripts

A collection of Ansible playbooks and roles for automating server setup and configuration across different environments.

## What's included

This repository contains playbooks for:
- **Hetzner Cloud**: Creating and configuring cloud servers
- **Proxmox**: Setting up LXC containers 
- **Generic Debian**: Configuring existing Debian/Ubuntu systems
- **Inventory Management**: Syncing hosts via Tailscale network

## Quick Start

1. **Install dependencies:**
   ```bash
   ansible-galaxy collection install -r requirements.yaml
   ```

2. **Set up 1Password CLI** for secret management (secrets are stored in 'CI' vault)

3. **Run a playbook:**
   ```bash
   # Create Hetzner server
   ansible-playbook create_hetzner.yaml
   
   # Create Proxmox LXC container
   ansible-playbook create_debian_lxc_pve.yaml
   
   # Configure existing server
   ansible-playbook setup_generic_debian_by_ip.yaml
   ```

## Features

All playbooks support optional installation of:
- **Docker** - Container runtime
- **Tailscale** - VPN networking
- **Backrest** - Backup solution
- **Monitoring tools** - System monitoring
- **Security hardening** - SSH, firewall, fail2ban

## Requirements

- Ansible 2.16+
- Python 3.8+
- 1Password CLI (for secrets)
- SSH access to target hosts

## Roles

The playbooks use these custom roles:
- `base-system` - Basic system setup
- `security` - SSH and firewall configuration
- `shell-config` - ZSH with Oh-My-Zsh
- `container-tools` - Docker installation
- `backup-tools` - Backrest backup setup
- `monitoring-tools` - System monitoring
- `pangolin-newt` - Custom API integration

## Inventory

Uses dynamic inventory via Tailscale (`inventories/tailscale.py`) to automatically discover hosts in your network.

## Security

- All secrets managed through 1Password lookups
- SSH key-based authentication only
- Automatic security updates
- Firewall configuration included

## Documentation

Each playbook has a corresponding `.md` file with detailed usage instructions.

## License

MIT