# Generic Debian System Setup by IP

This playbook (`setup-generic-debian-by-ip.yaml`) configures existing Debian-based systems by IP address, applying standardized configuration, security hardening, and optional service installation.

## Features

- Configures existing Debian systems via IP address
- Applies base system configuration and hardening
- Installs security updates and essential packages
- Configures SSH hardening and firewall rules
- Sets up ZSH with Oh-My-Zsh as default shell
- Installs optional components:
  - Docker container runtime
  - Tailscale VPN client
  - Backrest backup solution
  - Monitoring tools
  - Newt with Pangolin API integration

## Requirements

- SSH access to target Debian system
- Root or sudo privileges on target system
- 1Password CLI for secret management
- Network connectivity to target IP address
- Tailscale network access (optional)

## Variables

### Required Variables (prompted interactively)

- `target_ip`: IP address of the target system to configure
- `target_user`: SSH username for initial connection (default: root)
- `new_hostname`: New hostname to set for the system (optional)

### Feature Flags

- `docker`: Install Docker container runtime (True/False)
- `tailscale`: Install and configure Tailscale VPN (True/False)
- `backrest`: Install Backrest backup solution (True/False)
- `monitoring`: Install monitoring tools (True/False)
- `newt`: Install Newt with Pangolin API integration (True/False)

### SSH Configuration

- `ssh_port`: SSH port to configure (default: 22)
- `disable_root_login`: Disable SSH root login after setup (True/False)
- `create_admin_user`: Create a non-root admin user (True/False)
- `admin_username`: Username for admin user (if created)

### Secrets from 1Password

- SSH public keys for authorized access
- Tailscale authentication key (if enabled)
- Pangolin API token (if Newt is enabled)
- Admin user password (if admin user is created)

## Usage

### Interactive Mode

```bash
ansible-playbook playbooks/system/setup-generic-debian-by-ip.yaml
```

The playbook will prompt for the target IP address and configuration options.

### Non-Interactive Mode

```bash
ansible-playbook playbooks/system/setup-generic-debian-by-ip.yaml \
  -e "target_ip=192.168.1.100" \
  -e "target_user=debian" \
  -e "new_hostname=my-server" \
  -e "docker=true" \
  -e "tailscale=true"
```

### Using Variable Files

Create a variable file for repeated deployments:

```yaml
# vars/server-config.yaml
target_ip: "10.0.0.50"
target_user: "root"
new_hostname: "web-server-01"
docker: true
tailscale: true
backrest: true
monitoring: true
ssh_port: 2222
disable_root_login: true
create_admin_user: true
admin_username: "admin"
```

```bash
ansible-playbook playbooks/system/setup-generic-debian-by-ip.yaml \
  -e "@vars/server-config.yaml"
```

## Included Roles

This playbook applies the following roles in sequence:

1. **`base-system`**: Basic system configuration
   - Updates package cache and installs essential packages
   - Configures timezone and locale
   - Sets up proper DNS resolution
   - Configures system logging

2. **`security`**: Security hardening
   - SSH configuration and hardening
   - Firewall setup with UFW
   - Fail2ban installation and configuration
   - Automatic security updates

3. **`shell-config`**: Shell environment
   - Installs ZSH and Oh-My-Zsh
   - Configures shell aliases and environment
   - Sets ZSH as default shell for users

4. **`monitoring-tools`** (if enabled): System monitoring
   - Installs system monitoring tools
   - Configures log rotation
   - Sets up basic performance monitoring

5. **`backup-tools`** (if enabled): Backup configuration
   - Installs and configures Backrest
   - Sets up backup schedules
   - Configures backup retention policies

6. **`container-tools`** (if enabled): Docker setup
   - Installs Docker CE
   - Configures Docker daemon
   - Sets up Docker Compose
   - Configures container logging

7. **`pangolin-newt`** (if enabled): Pangolin API integration
   - Installs Newt application
   - Configures Pangolin API connection
   - Sets up service monitoring

## Supported Debian Versions

- Debian 11 (Bullseye)
- Debian 12 (Bookworm)
- Debian 13 (Trixie)
- Ubuntu 20.04 LTS (Focal)
- Ubuntu 22.04 LTS (Jammy)
- Ubuntu 24.04 LTS (Noble)

## Network Configuration

The playbook will:
- Preserve existing network configuration
- Configure firewall rules based on installed services
- Set up Tailscale networking (if enabled)
- Configure DNS settings for optimal resolution

## Security Hardening

Applied security measures include:
- SSH key-only authentication
- Disabled SSH root login (optional)
- UFW firewall with restrictive default policy
- Fail2ban for intrusion prevention
- Automatic security updates
- Proper file permissions and ownership
- System audit logging

## Post-Setup Verification

After successful execution, verify the setup:

1. **SSH access with new configuration:**
   ```bash
   ssh -p <ssh_port> <user>@<target_ip>
   ```

2. **Check installed services:**
   ```bash
   systemctl status docker    # If Docker was installed
   systemctl status tailscaled # If Tailscale was installed
   ```

3. **Verify firewall rules:**
   ```bash
   sudo ufw status verbose
   ```

4. **Test Tailscale connectivity (if enabled):**
   ```bash
   tailscale status
   tailscale ping <hostname>
   ```

## Troubleshooting

### Connection Issues
- **SSH connection refused**: Check if SSH service is running and port is correct
- **Permission denied**: Verify SSH keys are properly configured
- **Network unreachable**: Check network connectivity and firewall rules

### Package Installation Issues
- **Package not found**: Update package cache or check repository configuration
- **Dependency conflicts**: Review installed packages and resolve conflicts
- **Insufficient disk space**: Check available disk space before installation

### Service Configuration Issues
- **Docker installation fails**: Check system architecture compatibility
- **Tailscale authentication fails**: Verify auth key and network connectivity
- **Backup configuration errors**: Check storage permissions and paths

### Common Solutions
```bash
# Check system logs for errors
sudo journalctl -xe

# Verify network connectivity
ping 8.8.8.8

# Check SSH configuration
sudo sshd -t

# Review firewall status
sudo ufw status verbose

# Check service status
systemctl status <service-name>
```

## Best Practices

1. **Always backup** existing configuration before running
2. **Test on non-production systems** first
3. **Use SSH keys** instead of passwords
4. **Keep 1Password vault** up to date with current credentials
5. **Document any customizations** for future reference
6. **Monitor system logs** during and after execution
7. **Verify all services** are working after completion

## Integration with Other Playbooks

This playbook works well with:
- `update-inventory.yaml`: Add configured systems to dynamic inventory
- Infrastructure playbooks: Apply to newly created systems
- Backup playbooks: Configure centralized backup strategies

## Notes

- The playbook is idempotent and can be run multiple times safely
- Existing configurations are preserved where possible
- Failed tasks can be retried using Ansible's `--start-at-task` option
- All sensitive data is retrieved from 1Password for security