# Hetzner Cloud Server Creation and Configuration

This playbook (`create_hetzner.yaml`) automates the creation and configuration of servers in the Hetzner Cloud, with optional Pangolin API integration.

## Features

- Creates a new server in the Hetzner Cloud
- Configures the server with the following components:
  - Base system (updates, essential packages)
  - Security settings
  - ZSH as the default shell with Oh-My-Zsh
  - Monitoring tools
  - Optional: Docker
  - Optional: Backrest (backup solution)
  - Optional: Tailscale
  - Optional: Newt (with Pangolin API integration)

## Requirements

- Hetzner Cloud API token stored in 1Password
- 1Password CLI for secret management
- SSH key registered in Hetzner Cloud
- Tailscale (optional, for remote access)

## Variables

### Required Variables (prompted interactively)

- `server_name`: Name of the server to create
- `server_type`: Server type (e.g. cax11)
- `server_image`: OS image (e.g. debian-13)
- `server_location`: Location (e.g. fsn1)
- `ssh_keys`: Name of the SSH key in Hetzner Cloud

### Feature Flags

- `docker`: Enable Docker installation (True/False)
- `backrest`: Enable Backrest installation (True/False)
- `tailscale`: Enable Tailscale installation (True/False)
- `newt`: Enable Newt installation (True/False)

### Secrets from 1Password

- Hetzner Cloud API token
- Tailscale token for remote server
- Pangolin API token and credentials (if Newt is enabled)

## Usage

```bash
ansible-playbook create_hetzner.yaml
```

The playbook will interactively prompt for any required information not already defined as variables.

## Included Roles

This playbook uses the following roles:

- `base-system`: Basic system configuration
- `security`: Security settings
- `shell-config`: ZSH and Oh-My-Zsh configuration
- `monitoring-tools`: Monitoring tools
- `backup-tools`: Backup configuration (if enabled)
- `container-tools`: Docker installation (if enabled)
- `pangolin-newt`: Newt with Pangolin API integration (if enabled)

## Notes

- If Tailscale is enabled, SSH access over the public internet is disabled
- Existing servers with the same name in the Tailscale network are detected and reused
- After creation, the playbook waits until the server is reachable via SSH