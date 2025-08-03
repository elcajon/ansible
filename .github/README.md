# Ansible Server Management Playbooks

This repository contains Ansible playbooks and roles for automated server provisioning and management, with a focus on Hetzner Cloud infrastructure and Debian-based systems.

## Directory Structure

```
.
├── create_hetzner.yaml          # Main playbook for creating Hetzner Cloud servers
├── create_debian_lxc_pve.yaml   # Playbook for creating Debian LXC containers on Proxmox VE
├── test.yaml                    # Test playbook for server configuration
├── roles/
│   └── server-setup/            # Role for server configuration and setup
│       ├── tasks/
│       ├── defaults/
│       ├── handlers/
│       ├── meta/
│       ├── templates/
│       ├── tests/
│       └── vars/
└── inventories/
    └── tailscale.py             # Dynamic inventory script for Tailscale
```

## Usage

### Creating a Hetzner Cloud Server

The primary playbook `create_hetzner.yaml` automates the creation and setup of servers on Hetzner Cloud. This playbook:

- Creates a new server using the Hetzner Cloud API
- Configures the server with Tailscale for secure networking
- Applies the server-setup role for initial configuration

#### Prerequisites

1. Hetzner Cloud API token stored in OnePassword (vault: 'CI', item: 'Hetzner Cloud API')
2. Tailscale authentication token stored in OnePassword (vault: 'CI', item: 'Tailscale Token - Remote Server')
3. SSH key configured in your Hetzner Cloud project

#### Running the Playbook

```bash
ansible-playbook create_hetzner.yaml
```

The playbook will prompt you for the following information:
- **Server Name**: Name for the new server
- **Server Type**: Instance type (default: cax11)
- **Server Image**: OS image (default: debian-12)
- **Server Location**: Data center location (default: fsn1)
- **SSH Keys**: SSH key name from your Hetzner Cloud project (default: MBA SSH)
- **Docker**: Whether to install Docker (default: True)

#### Example Interactive Session

```
Name des zu erstellenden Servers eingeben: my-server
Server Type des zu erstellenden Servers eingeben [cax11]: 
Server Image des zu erstellenden Servers eingeben [debian-12]: 
Server Location des zu erstellenden Servers eingeben [fsn1]: 
SSH Key Name des zu erstellenden Servers eingeben [MBA SSH]: 
Docker installiert? [True/False] [True]: 
```

### Additional Playbooks

#### create_debian_lxc_pve.yaml
Creates Debian LXC containers on Proxmox VE infrastructure.

#### test.yaml
Test playbook for validating server configurations and backup setups.

## Roles

### server-setup
A comprehensive role that handles:
- Initial server configuration
- Software installation and updates
- Security hardening
- Service configuration
- Backup setup with pgbackrest

## Dependencies

This repository requires the following Ansible collections:
- `hetzner.hcloud` - For Hetzner Cloud API integration
- `community.general` - For OnePassword lookups and general utilities
- `artis3n.tailscale` - For Tailscale VPN setup

Install dependencies with:
```bash
ansible-galaxy collection install hetzner.hcloud community.general
ansible-galaxy install artis3n.tailscale
```

## Security Notes

- API tokens and sensitive data are stored in OnePassword and retrieved securely during playbook execution
- Servers are automatically configured with Tailscale for secure remote access
- SSH access is configured using predefined SSH keys
