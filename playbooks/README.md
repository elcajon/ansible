# Ansible Playbooks Collection

[![Playbooks](https://img.shields.io/badge/playbooks-4-blue)](.)
[![Infrastructure](https://img.shields.io/badge/infrastructure-2-green)](infrastructure/)
[![System Management](https://img.shields.io/badge/system-2-orange)](system/)
[![Documentation](https://img.shields.io/badge/docs-100%25-brightgreen)](.)

A comprehensive collection of Ansible playbooks for infrastructure automation, system configuration, and deployment management across multiple cloud providers and environments.

## 📋 Playbook Catalog

### 🏗️ Infrastructure Provisioning (`infrastructure/`)

| Playbook | Provider | Description | Status |
|----------|----------|-------------|---------|
| [`create-hetzner.yaml`](infrastructure/create-hetzner.yaml) | Hetzner Cloud | Automated server provisioning with full configuration | ✅ Production |
| [`create-debian-lxc-pve.yaml`](infrastructure/create-debian-lxc-pve.yaml) | Proxmox VE | LXC container creation and management | ✅ Production |

### ⚙️ System Management (`system/`)

| Playbook | Target | Description | Status |
|----------|---------|-------------|---------|
| [`setup-generic-debian-by-ip.yaml`](system/setup-generic-debian-by-ip.yaml) | Debian/Ubuntu | Complete system configuration and hardening | ✅ Production |
| [`update-inventory.yaml`](system/update-inventory.yaml) | Tailscale Network | Dynamic inventory synchronization | ✅ Production |

## 🚀 Quick Start Guide

### Prerequisites Checklist

- [ ] **Ansible 2.16+** with Python 3.8+
- [ ] **1Password CLI** configured with 'CI' vault access
- [ ] **SSH keys** configured for target infrastructure
- [ ] **Network access** to target environments
- [ ] **Required collections** installed (see below)

### Installation & Setup

1. **Install Ansible collections:**
   ```bash
   ansible-galaxy collection install -r requirements.yaml
   ```

2. **Verify 1Password CLI:**
   ```bash
   op account list
   op vault list
   # Ensure 'CI' vault is accessible
   ```

3. **Test configuration:**
   ```bash
   ansible-config dump --only-changed
   ansible-inventory --list
   ```

### First Deployment

```bash
# 🏗️ Infrastructure: Create a new Hetzner server
ansible-playbook playbooks/infrastructure/create-hetzner.yaml

# ⚙️ System: Configure existing Debian server
ansible-playbook playbooks/system/setup-generic-debian-by-ip.yaml

# 📋 Maintenance: Update inventory from network
ansible-playbook playbooks/system/update-inventory.yaml
```

## 📖 Detailed Documentation

### Infrastructure Playbooks

#### 🟦 Hetzner Cloud Server Creation
- **File**: `infrastructure/create-hetzner.yaml`
- **Documentation**: [📄 Complete Guide](infrastructure/create-hetzner.md)
- **Features**:
  - ☁️ Cloud instance provisioning
  - 🔧 Automated configuration pipeline
  - 🐳 Optional Docker installation
  - 🔒 Tailscale VPN integration
  - 💾 Backrest backup setup
  - 🌐 Pangolin API integration

#### 🟧 Proxmox LXC Container Management
- **File**: `infrastructure/create-debian-lxc-pve.yaml`
- **Documentation**: [📄 Complete Guide](infrastructure/create-debian-lxc-pve.md)
- **Features**:
  - 📦 Unprivileged container creation
  - 🌐 Network configuration
  - 🔧 Feature-specific container setup
  - 🚀 Service deployment automation

### System Management Playbooks

#### 🟩 Generic Debian Configuration
- **File**: `system/setup-generic-debian-by-ip.yaml`
- **Documentation**: [📄 Complete Guide](system/setup-generic-debian-by-ip.md)
- **Features**:
  - 🛡️ Security hardening
  - 📦 Package management
  - 👤 User configuration
  - 🔥 Firewall setup
  - 📊 Monitoring integration

#### 🟨 Inventory Synchronization
- **File**: `system/update-inventory.yaml`
- **Documentation**: [📄 Complete Guide](system/update-inventory.md)
- **Features**:
  - 🔄 Dynamic host discovery
  - 🏷️ Tag-based grouping
  - 📋 Inventory management
  - 🌐 Tailscale integration

## ⚙️ Configuration Patterns

### Interactive Mode (Default)
```bash
# Playbooks will prompt for required information
ansible-playbook playbooks/infrastructure/create-hetzner.yaml
# Prompts: server name, type, location, features, etc.
```

### Non-Interactive Mode
```bash
# Provide all variables via command line
ansible-playbook playbooks/infrastructure/create-hetzner.yaml \
  -e "server_name=web-01" \
  -e "server_type=cx21" \
  -e "server_location=fsn1" \
  -e "docker=true" \
  -e "tailscale=true"
```

### Variable Files
```bash
# Use external variable files for complex configurations
ansible-playbook playbooks/system/setup-generic-debian-by-ip.yaml \
  -e "@vars/production-config.yaml"
```

### Environment-Specific Deployments
```bash
# Target specific environments
ansible-playbook playbooks/infrastructure/create-hetzner.yaml \
  --limit production \
  -e "@group_vars/production.yaml"
```

## 🔧 Common Variables Reference

### Universal Feature Flags

| Variable | Type | Default | Description |
|----------|------|---------|-------------|
| `docker` | boolean | `false` | Install Docker runtime |
| `tailscale` | boolean | `false` | Configure Tailscale VPN |
| `backrest` | boolean | `false` | Setup backup solution |
| `newt` | boolean | `false` | Install Newt + Pangolin API |
| `monitoring` | boolean | `true` | Enable monitoring tools |

### Network Configuration

| Variable | Type | Example | Description |
|----------|------|---------|-------------|
| `server_name` | string | `web-server-01` | Server hostname |
| `network_ip_mask` | CIDR | `10.0.0.10/24` | IP address with netmask |
| `network_gateway` | IP | `10.0.0.1` | Network gateway |
| `network_dns` | IP | `1.1.1.1` | DNS server address |

### Provider-Specific Variables

#### Hetzner Cloud
```yaml
server_type: cx21        # Instance size
server_image: debian-12  # OS image
server_location: fsn1    # Datacenter
ssh_keys: [my-key]      # SSH key names
```

#### Proxmox VE
```yaml
proxmox_node: pve-01    # Target node
lxc_template: debian-12 # Container template
lxc_cores: 2           # CPU cores
lxc_memory: 2048       # RAM in MB
```

## 🔒 Security & Secrets Management

### 1Password Integration

All sensitive data is managed through 1Password:

```yaml
# Example secret lookup in playbooks
api_token: "{{ lookup('community.general.onepassword', 'API Token', field='credential', vault='CI') }}"
ssh_key: "{{ lookup('community.general.onepassword', 'SSH Key', field='public_key', vault='CI') }}"
```

### Required Secrets in 1Password 'CI' Vault

| Item | Fields | Usage |
|------|--------|-------|
| `Hetzner Cloud API` | `token` | Server provisioning |
| `Proxmox API` | `Host-Name`, `Benutzername`, `TokenID`, `Anmeldedaten` | Container management |
| `Tailscale Token` | `Anmeldedaten` | Network integration |
| `MBA SSH - Public Key` | `Benutzername` | SSH access |
| `Pangolin - Mannheim` | `Anmeldedaten`, `Host-Name`, `Organisation` | API integration |

### Security Best Practices

- ✅ **No hardcoded secrets** in playbooks or variables
- ✅ **SSH key-based authentication** only
- ✅ **Firewall-first approach** with restrictive defaults
- ✅ **Encrypted variable files** using Ansible Vault
- ✅ **Regular credential rotation** via 1Password

## 🧪 Testing & Validation

### Pre-Execution Checks

```bash
# Syntax validation
ansible-playbook --syntax-check playbooks/infrastructure/*.yaml

# Dry run mode
ansible-playbook --check --diff playbooks/system/setup-generic-debian-by-ip.yaml

# Lint validation
ansible-lint playbooks/

# YAML validation
yamllint playbooks/
```

### Development Workflow

1. **🔍 Syntax Check**: Validate YAML and Jinja2 syntax
2. **🧪 Dry Run**: Test without making changes
3. **🔧 Development**: Test in development environment
4. **📊 Validation**: Verify expected state
5. **🚀 Production**: Deploy to production environment

## 🏷️ Tagging Strategy

### Available Tags

| Tag | Scope | Description |
|-----|-------|-------------|
| `infrastructure` | Creation | Infrastructure provisioning tasks |
| `configuration` | Setup | System configuration tasks |
| `security` | Hardening | Security-related tasks |
| `networking` | Network | Network configuration |
| `monitoring` | Observability | Monitoring setup |
| `backup` | Data protection | Backup configuration |
| `docker` | Containers | Container runtime setup |
| `tailscale` | VPN | Tailscale configuration |

### Tag Usage Examples

```bash
# Run only security hardening
ansible-playbook playbooks/system/setup-generic-debian-by-ip.yaml --tags security

# Skip backup configuration
ansible-playbook playbooks/infrastructure/create-hetzner.yaml --skip-tags backup

# Run multiple specific tags
ansible-playbook playbooks/system/setup-generic-debian-by-ip.yaml --tags "security,networking,monitoring"
```

## 📊 Monitoring & Reporting

### Execution Monitoring

```bash
# Verbose output for debugging
ansible-playbook -vvv playbooks/infrastructure/create-hetzner.yaml

# JSON output for parsing
ansible-playbook --tree /tmp/ansible-logs playbooks/system/setup-generic-debian-by-ip.yaml

# Performance profiling
ANSIBLE_CALLBACK_WHITELIST=profile_tasks ansible-playbook playbooks/infrastructure/create-debian-lxc-pve.yaml
```

### Health Checks

```bash
# Verify all managed hosts
ansible all -m ping --inventory inventories/tailscale.py

# Check specific services
ansible all -m service -a "name=sshd state=started"

# Gather system information
ansible all -m setup --tree /tmp/facts
```

## 🔧 Troubleshooting Guide

### Common Issues & Solutions

#### Connection Failures
```bash
# Check SSH connectivity
ssh -T user@target-host

# Verify SSH key
ansible all -m ping --inventory inventories/tailscale.py -u root --key-file ~/.ssh/id_rsa

# Test with different SSH options
ansible all -m ping --ssh-extra-args="-o UserKnownHostsFile=/dev/null -o StrictHostKeyChecking=no"
```

#### Authentication Issues
```bash
# Verify 1Password CLI
op item list --vault CI

# Test secret lookups
ansible localhost -m debug -a "var=lookup('community.general.onepassword', 'Test Item', field='password', vault='CI')"

# Check vault access
op vault list | grep CI
```

#### Role/Collection Issues
```bash
# Reinstall collections
ansible-galaxy collection install --force -r requirements.yaml

# List installed collections
ansible-galaxy collection list

# Check role paths
ansible-config dump | grep ROLES_PATH
```

#### Performance Issues
```bash
# Enable SSH multiplexing
export ANSIBLE_SSH_ARGS="-o ControlMaster=auto -o ControlPersist=60s"

# Increase parallelism
ansible-playbook --forks 20 playbooks/system/setup-generic-debian-by-ip.yaml

# Use strategy plugins
ansible-playbook --strategy free playbooks/infrastructure/create-hetzner.yaml
```

### Debug Mode

```bash
# Enable debug output
ANSIBLE_DEBUG=1 ansible-playbook playbooks/infrastructure/create-hetzner.yaml

# Log all task results
ANSIBLE_KEEP_REMOTE_FILES=1 ansible-playbook playbooks/system/setup-generic-debian-by-ip.yaml
```

## 🤝 Contributing

### Development Guidelines

1. **📝 Documentation**: Every playbook must have comprehensive documentation
2. **🧪 Testing**: All changes must pass syntax and lint checks
3. **🏷️ Tagging**: Implement appropriate task tags
4. **🔒 Security**: Never commit secrets or credentials
5. **📊 Logging**: Include meaningful task names and debug output

### Code Standards

- **YAML Style**: 2-space indentation, no trailing spaces
- **Variable Naming**: `snake_case` for variables, `kebab-case` for files
- **Task Names**: Descriptive and action-oriented
- **Comments**: Explain complex logic and decisions
- **Error Handling**: Implement proper error handling and rollback

### Review Process

1. **🔍 Pre-commit**: Run syntax and lint checks
2. **📋 Self-review**: Test in development environment
3. **👥 Peer review**: Submit for team review
4. **✅ Integration**: Automated testing pipeline
5. **🚀 Deployment**: Merge and deploy

---

**🎯 Next Steps**: Choose a playbook above and follow its documentation for detailed implementation guidance.

**🆘 Need Help?** Check the [troubleshooting section](#-troubleshooting-guide) or refer to individual playbook documentation.