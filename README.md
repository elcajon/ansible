# Ansible Infrastructure Automation Platform

[![CI](https://github.com/yourusername/ansible-infrastructure/workflows/CI/badge.svg)](https://github.com/yourusername/ansible-infrastructure/actions)
[![Security](https://github.com/yourusername/ansible-infrastructure/workflows/Security/badge.svg)](https://github.com/yourusername/ansible-infrastructure/actions)
[![Ansible](https://img.shields.io/badge/ansible-2.16%2B-blue)](https://docs.ansible.com/)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)

A comprehensive Ansible-based infrastructure automation platform for managing cloud infrastructure, container deployments, and system configurations across multiple environments.

## 🏗️ Architecture Overview

This platform provides a structured approach to infrastructure management with:

- **Modular Playbooks**: Organized by function (infrastructure creation vs. system management)
- **Reusable Roles**: Common functionality packaged for consistency
- **Dynamic Inventory**: Auto-discovery via Tailscale network integration
- **Secret Management**: Secure credential handling through 1Password
- **Multi-Environment**: Support for development, staging, and production deployments

## 📁 Project Structure

```
ansible/
├── 📚 docs/                      # Central documentation hub
├── 🏠 inventories/               # Dynamic and static inventory management
├── 🎭 playbooks/                 # Organized automation playbooks
│   ├── 🏗️  infrastructure/       # Cloud resource provisioning
│   └── ⚙️  system/               # Configuration management
├── 🧩 roles/                     # Reusable automation components
├── 👥 group_vars/                # Environment and group configurations
├── 🖥️  host_vars/                # Host-specific configurations
├── 📋 requirements.yaml          # Dependencies and collections
└── ⚙️  ansible.cfg               # Platform configuration
```

## 🚀 Quick Start

### Prerequisites

- **Ansible**: 2.16+ with Python 3.8+
- **1Password CLI**: For secure secret management
- **Tailscale**: For network connectivity and dynamic inventory
- **SSH
 Access**: To target infrastructure

### Installation

1. **Clone and setup:**
   ```bash
   git clone <repository-url>
   cd ansible
   ```

2. **Install dependencies:**
   ```bash
   ansible-galaxy collection install -r requirements.yaml
   ```

3. **Configure 1Password CLI:**
   ```bash
   # Install 1Password CLI for your platform
   # https://developer.1password.com/docs/cli/get-started/
   op account add
   op signin
   # Ensure access to 'CI' vault
   ```

4. **Verify configuration:**
   ```bash
   ansible --version
   ansible-config dump --only-changed
   ```

### First Deployment

```bash
# Create a new Hetzner Cloud server
ansible-playbook playbooks/infrastructure/create-hetzner.yaml

# Configure an existing Debian system
ansible-playbook playbooks/system/setup-generic-debian-by-ip.yaml

# Update inventory from Tailscale network
ansible-playbook playbooks/system/update-inventory.yaml
```

## 📖 Documentation

### Infrastructure Playbooks

| Playbook | Description | Documentation |
|----------|-------------|---------------|
| `create-hetzner.yaml` | Hetzner Cloud server provisioning | [📄](playbooks/infrastructure/create-hetzner.md) |
| `create-debian-lxc-pve.yaml` | Proxmox LXC container creation | [📄](playbooks/infrastructure/create-debian-lxc-pve.md) |

### System Management Playbooks

| Playbook | Description | Documentation |
|----------|-------------|---------------|
| `setup-generic-debian-by-ip.yaml` | Debian system configuration | [📄](playbooks/system/setup-generic-debian-by-ip.md) |
| `update-inventory.yaml` | Dynamic inventory management | [📄](playbooks/system/update-inventory.md) |

### Component Documentation

- **📚 [Playbooks Overview](playbooks/README.md)** - Detailed playbook documentation
- **🏠 [Inventory Management](inventories/README.md)** - Dynamic and static inventory
- **👥 [Group Variables](group_vars/README.md)** - Environment configuration
- **🖥️ [Host Variables](host_vars/README.md)** - Host-specific settings
- **📋 [Central Documentation](docs/README.md)** - Architecture and guides

## 🧩 Roles Ecosystem

| Role | Purpose | Key Features |
|------|---------|--------------|
| `base-system` | Foundation setup | Package management, timezone, users |
| `security` | Hardening | SSH config, firewall, fail2ban |
| `shell-config` | User environment | ZSH, Oh-My-Zsh, aliases |
| `monitoring-tools` | Observability | System metrics, log management |
| `backup-tools` | Data protection | Backrest integration, scheduling |
| `container-tools` | Containerization | Docker, Docker Compose |
| `proxmox-setup` | Virtualization | Proxmox-specific configurations |
| `pangolin-newt` | API integration | Newt app with Pangolin API |

## 🔒 Security & Secrets

### Secret Management Strategy

- **🔐 1Password Integration**: All sensitive data stored in '1Password CI' vault
- **🚫 No Hardcoded Secrets**: Zero credentials in version control
- **🔑 Key-Based Authentication**: SSH keys only, no passwords
- **🛡️ Encrypted Variables**: Ansible Vault for sensitive configurations

### Security Features

- **🔥 Firewall Configuration**: UFW with restrictive defaults
- **🚪 SSH Hardening**: Key-only auth, custom ports, fail2ban
- **🔄 Automatic Updates**: Security patches applied automatically
- **📊 Audit Logging**: Comprehensive system activity tracking

## 🌍 Multi-Environment Support

### Environment Structure

```yaml
# Example: group_vars/production.yaml
environment: production
log_level: warn
backup_retention_days: 30
monitoring_enabled: true
debug_mode: false
```

### Deployment Patterns

```bash
# Environment-specific deployments
ansible-playbook playbooks/infrastructure/create-hetzner.yaml -e @group_vars/production.yaml

# Targeted group deployments
ansible-playbook playbooks/system/setup-generic-debian-by-ip.yaml --limit production

# Tag-based execution
ansible-playbook playbooks/system/setup-generic-debian-by-ip.yaml --tags security,monitoring
```

## 🔧 Development Workflow

### Contributing Guidelines

1. **🔍 Code Review**: All changes require review
2. **✅ Testing**: Syntax and lint checks mandatory
3. **📖 Documentation**: Update docs with changes
4. **🏷️ Naming**: Use kebab-case for files,
 snake_case for variables
5. **🔐 Security**: Never commit secrets or credentials

### Quality Assurance

```bash
# Syntax validation
ansible-playbook --syntax-check playbooks/infrastructure/*.yaml

# Linting
ansible-lint .

# Security scanning
yamllint .
```

### Testing Strategy

- **🧪 Syntax Validation**: Automated YAML and Jinja2 checking
- **🔍 Security Scanning**: Credential and vulnerability detection  
- **📊 Role Testing**: Individual role functionality verification
- **🏗️ Integration Testing**: End-to-end deployment validation

## 📊 Monitoring & Observability

### Built-in Monitoring

- **📈 System Metrics**: Resource utilization tracking
- **📝 Centralized Logging**: Structured log aggregation
- **🚨 Alerting**: Proactive issue notification
- **📋 Inventory Tracking**: Asset and configuration management

### Health Checks

```bash
# Verify infrastructure health
ansible all -m ping --inventory inventories/tailscale.py

# Check service status
ansible all -m service -a "name=sshd state=started" --limit production

# Gather system facts
ansible-playbook playbooks/system/health-check.yaml
```

## 🤝 Support & Community

### Getting Help

- **📖 Documentation**: Start with playbook-specific docs
- **🐛 Issues**: Report bugs via GitHub issues
- **💬 Discussions**: Community support and feature requests
- **📧 Security**: Report vulnerabilities privately

### Troubleshooting

Common issues and solutions:

| Problem | Solution | Reference |
|---------|----------|-----------|
| Connection failures | Check SSH keys and network | [SSH Setup Guide](docs/troubleshooting.md#ssh) |
| 1Password auth errors | Verify CLI setup and vault access | [1Password Setup](docs/setup.md#1password) |
| Role not found | Install missing collections | [Dependencies](requirements.yaml) |
| Tailscale inventory empty | Check network connectivity | [Inventory Guide](inventories/README.md) |

## 📄 License & Legal

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

### Third-Party Components

- **Ansible**: GPL v3+ - Configuration management platform
- **1Password CLI**: Proprietary - Secret management integration  
- **Tailscale**: BSD 3-Clause - Network connectivity and discovery

---

**🚀 Ready to automate your infrastructure?** Start with the [Quick Start](#-quick-start) guide above!