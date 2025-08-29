# Proxmox LXC Container Creation and Configuration

This playbook (`create-debian-lxc-pve.yaml`) automates the creation and configuration of Debian-based LXC containers in a Proxmox environment.

## Features

- Creates a new LXC container on Proxmox
- Configures network settings
- Installs and configures optional components:
  - Docker
  - Tailscale
  - Backrest (backup solution)
  - Newt (with Pangolin API integration)
- Sets up basic security settings
- Sets ZSH as the default shell with Oh-My-Zsh
- Installs monitoring tools

## Requirements

- Access to a Proxmox host
- 1Password CLI for secret management
- SSH access to the Proxmox host for container configuration
- Tailscale (optional, for remote access)

## Variables

### Required Variables (prompted interactively)

- `server_name`: Name of the server to create
- `lxc_pw`: Password for the root user
- `network_ip_mask`: IP address and netmask (e.g. 10.10.0.5/23)
- `network_gateway`: Gateway address
- `network_dns`: DNS server address

### Feature Flags

- `docker`: Enable Docker installation (True/False)
- `backrest`: Enable Backrest installation (True/False)
- `tailscale`: Enable Tailscale installation (True/False)
- `newt`: Enable Newt installation (True/False)

### Secrets from 1Password

- Proxmox API credentials
- SSH public key
- Tailscale token

## Usage

```bash
ansible-playbook playbooks/infrastructure/create-debian-lxc-pve.yaml
```

The playbook will interactively prompt for any required information not already defined as variables.

### Non-Interactive Usage

```bash
ansible-playbook playbooks/infrastructure/create-debian-lxc-pve.yaml \
  -e "server_name=my-container" \
  -e "lxc_pw=secure_password" \
  -e "network_ip_mask=10.10.0.5/23" \
  -e "network_gateway=10.10.0.1" \
  -e "network_dns=10.10.0.1" \
  -e "docker=true" \
  -e "tailscale=true"
```

### Using Variable Files

Create a variable file for repeated deployments:

```yaml
# vars/lxc-config.yaml
server_name: "docker-host"
network_ip_mask: "10.10.0.10/23"
network_gateway: "10.10.0.1"
network_dns: "10.10.0.1"
docker: true
tailscale: true
backrest: true
newt: false
```

```bash
ansible-playbook playbooks/infrastructure/create-debian-lxc-pve.yaml \
  -e "@vars/lxc-config.yaml" \
  --ask-vault-pass
```

## Container Specifications

### Default Configuration
- **OS Template**: debian-12-standard (latest Debian 12)
- **CPU Cores**: 2
- **Memory**: 2048 MB
- **Swap**: 512 MB
- **Disk**: 8 GB
- **Privilege**: Unprivileged container
- **Features**: 
  - `nesting=1` (if Docker enabled)
  - `keyctl=1` (if Docker enabled)
  - `fuse=1` (if Docker enabled)

### Network Configuration
- **Bridge**: vmbr0 (default Proxmox bridge)
- **Static IP**: As specified in variables
- **IPv6**: Enabled with DHCP
- **Firewall**: Enabled with restrictive rules

## Included Roles

This playbook uses the following roles:

- `base-system`: Basic system configuration
- `security`: Security settings
- `shell-config`: ZSH and Oh-My-Zsh configuration
- `monitoring-tools`: Monitoring tools
- `backup-tools`: Backup configuration (if enabled)
- `container-tools`: Docker installation (if enabled)
- `pangolin-newt`: Newt with Pangolin API integration (if enabled)

## Container Features

### Docker Support
When Docker is enabled, the container is configured with:
- Nesting capability for running containers
- Access to kernel keyrings
- FUSE filesystem support
- Proper cgroup configuration

### Tailscale Integration
When Tailscale is enabled:
- TUN device access is configured
- Tailscale daemon is installed and configured
- Container joins the Tailscale network automatically
- SSH access via Tailscale IP is enabled

### Backup Configuration
When Backrest is enabled:
- Backup client is installed and configured
- Scheduled backups are set up
- Retention policies are applied
- Integration with central backup server

## Proxmox Integration

### API Authentication
The playbook uses Proxmox API tokens stored in 1Password:
- API User: Retrieved from 1Password vault
- Token ID: Retrieved from 1Password vault
- Token Secret: Retrieved from 1Password vault

### Node Configuration
- **Target Node**: proxmox (configurable)
- **Storage**: local-lvm (default for container disks)
- **Template Storage**: local (for OS templates)
- **Network Bridge**: vmbr0 (default)

## Post-Creation Configuration

After container creation, the playbook:

1. **Waits for container to start** and become accessible
2. **Updates package cache** and installs security updates
3. **Applies base system configuration** via roles
4. **Configures networking** and firewall rules
5. **Installs optional services** based on feature flags
6. **Sets up monitoring** and logging
7. **Configures backups** if enabled
8. **Joins Tailscale network** if enabled

## Network Architecture

```
Internet
    │
    ├── Proxmox Host (Public IP)
    │   └── vmbr0 Bridge
    │       └── LXC Container (Private IP)
    │           └── Tailscale Interface (if enabled)
    │               └── Tailscale Network Access
```

## Security Considerations

- **Unprivileged containers**: All containers are created as unprivileged by default
- **SSH hardening**: Root login disabled, key-based authentication only
- **Firewall**: UFW configured with restrictive default policy
- **Updates**: Automatic security updates enabled
- **Secrets**: All sensitive data retrieved from 1Password

## Troubleshooting

### Container Creation Issues
- **API authentication failed**: Check Proxmox API credentials in 1Password
- **Template not found**: Ensure debian-12-standard template is downloaded
- **Storage full**: Check available storage on Proxmox node
- **Network conflict**: Verify IP address is not already in use

### Container Configuration Issues
- **SSH connection failed**: Check network configuration and firewall
- **Docker installation failed**: Verify container features are properly set
- **Tailscale connection failed**: Check auth key and network connectivity
- **Role execution failed**: Review Ansible logs for specific errors

### Common Solutions
```bash
# Check container status on Proxmox
pct status <container-id>

# View container configuration
pct config <container-id>

# Access container console
pct enter <container-id>

# Check container logs
journalctl -u <service-name>
```

## Best Practices

1. **Plan IP addressing** before creating multiple containers
2. **Use descriptive names** for easy identification
3. **Enable backups** for important containers
4. **Monitor resource usage** and adjust as needed
5. **Keep templates updated** for security patches
6. **Document container purposes** and configurations
7. **Test connectivity** after creation

## Integration with Other Playbooks

- Use `setup-generic-debian-by-ip.yaml` for additional configuration
- Use `update-inventory.yaml` to add to dynamic inventory
- Combine with monitoring playbooks for centralized oversight

## Notes

- The container is created as an unprivileged container by default
- If Docker is enabled, necessary container features are set (nesting=1, keyctl=1)
- If Tailscale is enabled, TUN device access is configured
- All network configuration is preserved during container lifecycle
- Container can be managed through both Proxmox web UI and Ansible