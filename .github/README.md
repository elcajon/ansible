# 🚀 Ansible Infrastructure Automation Platform

[![CI Pipeline](https://img.shields.io/badge/CI-automated-brightgreen)](workflows/)
[![Security Scanning](https://img.shields.io/badge/security-scanned-blue)](workflows/)
[![Ansible](https://img.shields.io/badge/ansible-2.16%2B-red)](https://docs.ansible.com/)
[![Python](https://img.shields.io/badge/python-3.8%2B-blue)](https://python.org/)
[![License](https://img.shields.io/badge/license-MIT-green)](../LICENSE)

A comprehensive, enterprise-grade Ansible automation platform for infrastructure provisioning, system configuration, and deployment management across multiple cloud providers and environments.

## 🏗️ What This Platform Provides

### Infrastructure as Code
- **☁️ Multi-Cloud Support**: Hetzner Cloud, Proxmox VE, and extensible architecture
- **🤖 Automated Provisioning**: Zero-touch server and container deployment
- **🔧 Configuration Management**: Consistent system setup across environments
- **📊 Dynamic Inventory**: Auto-discovery via Tailscale network integration

### Enterprise Features
- **🔒 Security-First**: 1Password integration, SSH hardening, firewall automation
- **📈 Scalable**: Support from single servers to large infrastructures
- **🔄 Idempotent**: Safe to run repeatedly, only makes necessary changes
- **📱 Multi-Platform**: Linux, containers, virtual machines

## 📋 Supported Platforms & Services

### Cloud Providers
- **Hetzner Cloud** - Virtual server provisioning and management
- **Proxmox VE** - LXC container creation and configuration

### Operating Systems
- **Debian 11/12/13** - Primary target with full feature support
- **Ubuntu 20.04/22.04/24.04** - LTS versions with complete compatibility

### Container Platforms
- **Docker** - Container runtime with Docker Compose
- **LXC** - System containers on Proxmox infrastructure

## 🎯 Quick Start

### Prerequisites
```bash
# System requirements
- Ansible 2.16+
- Python 3.8+
- 1Password CLI
- SSH key access
```

### Installation
```bash
# 1. Clone repository
git clone <repository-url>
cd ansible

# 2. Install dependencies
ansible-galaxy collection install -r requirements.yaml

# 3. Configure 1Password CLI
op account add
op signin

# 4. Verify setup
ansible --version && ansible-config dump --only-changed
```

### First Deployment
```bash
# Create Hetzner Cloud server
ansible-playbook playbooks/infrastructure/create-hetzner.yaml

# Configure existing Debian system  
ansible-playbook playbooks/system/setup-generic-debian-by-ip.yaml

# Update network inventory
ansible-playbook playbooks/system/update-inventory.yaml
```

## 📚 Documentation

### 🏗️ Infrastructure Provisioning
| Playbook | Provider | Documentation | Features |
|----------|----------|---------------|----------|
| `create-hetzner.yaml` | Hetzner Cloud | [📖 Guide](../playbooks/infrastructure/create-hetzner.md) | Server provisioning, networking, security |
| `create-debian-lxc-pve.yaml` | Proxmox VE | [📖 Guide](../playbooks/infrastructure/create-debian-lxc-pve.md) | LXC containers, feature configuration |

### ⚙️ System Management  
| Playbook | Target | Documentation | Features |
|----------|---------|---------------|----------|
| `setup-generic-debian-by-ip.yaml` | Debian/Ubuntu | [📖 Guide](../playbooks/system/setup-generic-debian-by-ip.md) | Complete system setup, hardening |
| `update-inventory.yaml` | Network | [📖 Guide](../playbooks/system/update-inventory.md) | Dynamic host discovery |

### 📖 Comprehensive Guides
- **🎯 [Main Documentation](../README.md)** - Platform overview and architecture
- **📋 [Playbook Collection](../playbooks/README.md)** - Complete automation catalog  
- **🏠 [Inventory Management](../inventories/README.md)** - Dynamic and static inventory
- **📚 [Documentation Hub](../docs/README.md)** - Central navigation and guides

## 🧩 Role Ecosystem

### Core System Roles
| Role | Purpose | Key Features |
|------|---------|--------------|
| `base-system` | Foundation | Package management, timezone, users, essential tools |
| `security` | Hardening | SSH configuration, firewall, fail2ban, audit logging |
| `shell-config` | User Environment | ZSH, Oh-My-Zsh, aliases, developer tools |

### Service & Application Roles
| Role | Purpose | Key Features |
|------|---------|--------------|
| `monitoring-tools` | Observability | System metrics, log management, health checks |
| `backup-tools` | Data Protection | Backrest integration, automated scheduling, retention |
| `container-tools` | Containerization | Docker, Docker Compose, container orchestration |

### Specialized Integration Roles
| Role | Purpose | Key Features |
|------|---------|--------------|
| `proxmox-setup` | Virtualization | Proxmox-specific configurations, LXC optimization |
| `pangolin-newt` | API Integration | Newt application deployment, Pangolin API connectivity |

## 🔒 Security Architecture

### Multi-Layer Security Model
```
🔐 1Password Vault (Secrets)
    ↓
🔑 SSH Key Authentication  
    ↓
🛡️ Firewall + Fail2ban
    ↓
🔍 Audit Logging
    ↓
🚨 Monitoring & Alerting
```

### Security Features
- **Zero Hardcoded Secrets**: All credentials managed via 1Password
- **Key-Based Authentication**: SSH keys only, no password authentication
- **Network Security**: UFW firewall with restrictive defaults
- **Intrusion Prevention**: Automated fail2ban configuration
- **Audit Trail**: Comprehensive logging and monitoring

## 🌍 Multi-Environment Support

### Environment Strategy
```yaml
# Development
environment: development
debug_mode: true
log_level: debug

# Production  
environment: production
debug_mode: false
log_level: warn
backup_retention_days: 30
```

### Deployment Patterns
```bash
# Environment-specific deployments
ansible-playbook playbooks/infrastructure/create-hetzner.yaml \
  -e "@group_vars/production.yaml"

# Targeted group operations
ansible-playbook playbooks/system/setup-generic-debian-by-ip.yaml \
  --limit production

# Tag-based execution
ansible-playbook playbooks/system/setup-generic-debian-by-ip.yaml \
  --tags "security,monitoring"
```

## 🧪 Quality Assurance

### Automated Testing Pipeline
- **✅ Syntax Validation**: YAML and Jinja2 template verification
- **🔍 Security Scanning**: Credential leak detection and vulnerability assessment  
- **📋 Lint Checking**: Ansible best practices enforcement
- **🧪 Integration Testing**: End-to-end deployment verification

### Quality Gates
```bash
# Pre-commit validation
ansible-playbook --syntax-check playbooks/**/*.yaml
ansible-lint .
yamllint .

# Security verification
trufflehog --only-verified .
```

## 📊 Platform Metrics

### Automation Coverage
- **🎭 4 Production Playbooks** - Covering all major use cases
- **🧩 8 Reusable Roles** - Modular, tested components  
- **🏠 Dynamic Inventory** - Auto-discovery and management
- **📚 100% Documentation Coverage** - Every component documented

### Supported Infrastructure
- **☁️ Multi-Cloud Ready** - Hetzner Cloud, Proxmox, extensible
- **🖥️ Operating Systems** - Debian, Ubuntu with full compatibility
- **📦 Container Platforms** - Docker, LXC with orchestration
- **🌐 Network Integration** - Tailscale VPN with dynamic discovery

## 🤝 Contributing

### Development Workflow
1. **🔍 Review** - Read documentation and understand architecture
2. **🧪 Test** - Validate changes in development environment  
3. **📖 Document** - Update documentation for any changes
4. **🔒 Security** - Ensure no secrets in commits
5. **✅ Quality** - Pass all linting and testing requirements

### Code Standards
- **YAML Style**: 2-space indentation, consistent formatting
- **Variable Naming**: `snake_case` variables, `kebab-case` files  
- **Documentation**: Comprehensive guides for all features
- **Testing**: Syntax validation and integration testing
- **Security**: 1Password for secrets, Ansible Vault for configs

## 📞 Support & Community

### Getting Help
| Type | Channel | Response Time |
|------|---------|---------------|
| **Usage Questions** | GitHub Discussions | 1-2 business days |
| **Bug Reports** | GitHub Issues | 1 business day |
| **Security Issues** | Private disclosure | 4 hours |
| **Feature Requests** | GitHub Issues | 1 week |

### Resources
- **📖 [Complete Documentation](../README.md)** - Comprehensive platform guide
- **🎯 [Quick Start Guide](../README.md#-quick-start)** - Get up and running fast
- **🔧 [Troubleshooting](../docs/README.md#-troubleshooting)** - Common issues and solutions  
- **🧩 [Role Documentation](../roles/)** - Individual component guides

## 📄 License & Legal

**License**: MIT License - see [LICENSE](../LICENSE) for details

### Third-Party Acknowledgments
- **Ansible** - Configuration management platform (GPL v3+)
- **1Password CLI** - Secret management integration (Proprietary)
- **Tailscale** - Network connectivity and discovery (BSD 3-Clause)

---

**🚀 Ready to automate your infrastructure?** 

👉 **Start with**: [Platform Overview](../README.md) → [Choose a Playbook](../playbooks/README.md) → **Deploy!**

**🎯 Enterprise Support Available** - Contact us for professional services, custom integrations, and enterprise support plans.