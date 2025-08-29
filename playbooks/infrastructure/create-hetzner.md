# Hetzner Cloud Server Creation

This playbook (`create-hetzner.yaml`) automates the creation and configuration of servers on Hetzner Cloud with Pangolin API integration.

## Features

- Creates new Hetzner Cloud instances
- Configures basic networking and SSH access
- Integrates with Pangolin API for server management
- Installs and configures base system components:
  - Docker (optional)
  - Tailscale VPN (optional)
  - Backrest backup solution (optional)
  - Newt with Pangolin API integration (optional)
- Sets up security hardening
- Configures ZSH with Oh-My-Zsh
- Installs monitoring tools

## Requirements

- Hetzner Cloud API token
- 1Password CLI for secret management
- SSH keys configured in Hetzner Cloud
- Pangolin API access token
- Tailscale network (optional, for VPN access)

## Variables

### Required Variables (prompted interactively)

- `server_name`: Name of the server to create
- `server_type`: Hetzner Cloud server type (e.g., cax11, cx21, cpx31)
- `server_image`: Operating system image (default: debian-13)
- `server_location`: Datacenter location (default: fsn1)
- `ssh_keys`: SSH key names configured in Hetzner Cloud

### Feature Flags

- `docker`: Enable Docker installation (True/False)
- `backrest`: Enable Backrest backup solution (True/False)
- `tailscale`: Enable Tailscale VPN (True/False)
- `newt`: Enable Newt with Pangolin API integration (True/False)

### Secrets from 1Password

- Hetzner Cloud API token
- Pangolin API token
- Tailscale authentication key (if enabled)

## Usage

```bash
ansible-playbook playbooks/infrastructure/create-hetzner.yaml
```

The playbook will interactively prompt for server configuration options.

### Non-Interactive Usage

You can provide variables via command line:

```bash
ansible-playbook playbooks/infrastructure/create-hetzner.yaml \
  -e "server_name=my-server" \
  -e "server_type=cx21" \
  -e "server_location=nbg1" \
  -e "ssh_keys=my-ssh-key"
```

## Included Roles

This playbook uses the following roles after server creation:

- `base-system`: Basic system configuration and hardening
- `security`: SSH and firewall security settings
- `shell-config`: ZSH and Oh-My-Zsh configuration
- `monitoring-tools`: System monitoring setup
- `backup-tools`: Backup configuration (if enabled)
- `container-tools`: Docker installation (if enabled)
- `pangolin-newt`: Newt application with Pangolin API integration (if enabled)

## Server Types

Common Hetzner Cloud server types:

| Type | CPU | RAM | Disk | Network | Price/Month |
|------|-----|-----|------|---------|-------------|
| cax11 | 1 vCPU | 4 GB | 40 GB | 20 TB | ~€4 |
| cx21 | 2 vCPU | 8 GB | 40 GB | 20 TB | ~€6 |
| cpx31 | 4 vCPU | 8 GB | 160 GB | 20 TB | ~€13 |
| cpx41 | 8 vCPU | 16 GB | 240 GB | 20 TB | ~€26 |

## Locations

Available datacenter locations:

- `fsn1`: Falkenstein, Germany
- `nbg1`: Nuremberg, Germany  
- `hel1`: Helsinki, Finland
- `ash`: Ashburn, VA, USA
- `hil`: Hillsboro, OR, USA

## Network Configuration

The server will be created with:
- Public IPv4 and IPv6 addresses
- SSH access on port 22
- Firewall configured based on installed services
- Tailscale integration (if enabled) for secure access

## Pangolin API Integration

If Newt is enabled, the server will be registered with the Pangolin API for centralized management and monitoring.

## Post-Creation Access

After successful creation, you can access your server:

1. **Direct SSH access:**
   ```bash
   ssh root@<server-ip>
   ```

2. **Via Tailscale (if enabled):**
   ```bash
   ssh root@<server-name>.tailscale.internal
   ```

3. **Add to inventory:**
   The server will be automatically discoverable via the Tailscale inventory script.

## Troubleshooting

- **API authentication errors**: Verify Hetzner Cloud API token in 1Password
- **SSH key not found**: Ensure SSH keys are uploaded to Hetzner Cloud dashboard
- **Location not available**: Check Hetzner Cloud status page for datacenter availability
- **Server type not available**: Some server types may be sold out in specific locations
- **Pangolin API errors**: Verify Pangolin API token and network connectivity

## Notes

- Servers are created with automatic backups disabled by default
- IPv6 is enabled by default
- Server deletion protection is enabled
- All sensitive configuration is retrieved from 1Password
- The playbook will fail if the server name already exists