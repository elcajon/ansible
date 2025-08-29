# Generic Debian Server Configuration by IP or Hostname

This playbook (`setup_generic_debian_by_ip.yaml`) automates the configuration of an existing Debian-based server that is reachable via IP address or hostname.

## Features

- Configures an existing Debian-based server
- Supports both direct IP/hostname connection and Tailscale connection
- Installs and configures optional components:
  - Docker
  - Tailscale
  - Backrest (backup solution)
  - Newt (with Pangolin API integration)
  - Proxmox
- Sets up basic security settings
- Sets ZSH as the default shell with Oh-My-Zsh
- Installs monitoring tools

## Requirements

- Root access to the target server
- SSH access to the server
- 1Password CLI for secret management
- Tailscale (optional, for remote access)

## Variables

### Required Variables (prompted interactively)

- `server_name`: DNS name or IP address of the server to configure

### Feature Flags

- `docker`: Enable Docker installation (True/False)
- `backrest`: Enable Backrest installation (True/False)
- `tailscale`: Enable Tailscale installation (True/False)
- `proxmox`: Enable Proxmox-specific configuration (True/False)
- `newt`: Enable Newt installation (True/False)

### Secrets from 1Password

- Tailscale token for remote server
- Pangolin API token and credentials (if Newt is enabled)

## Usage

```bash
ansible-playbook setup_generic_debian_by_ip.yaml
```

The playbook will interactively prompt for any required information not already defined as variables.

## Included Roles

This playbook uses the following roles:

- `base-system`: Basic system configuration with timezone Europe/Berlin
- `security`: Security settings
- `shell-config`: ZSH and Oh-My-Zsh configuration
- `monitoring-tools`: Monitoring tools
- `backup-tools`: Backup configuration (if enabled)
- `container-tools`: Docker installation (if enabled)
- `pangolin-newt`: Newt with Pangolin API integration (if enabled)
- `proxmox-setup`: Proxmox-specific configuration (if enabled)

## Notes

- If Tailscale is enabled, SSH access over the public internet is disabled
- The playbook checks if the server is already reachable via Tailscale and uses that connection if available
- Configuration is applied for the root user