# Group Variables

This directory contains variable files that apply to groups of hosts defined in your inventory.

## Structure

Group variable files should be named after the inventory groups they apply to:

```
group_vars/
├── all.yaml              # Variables for all hosts
├── webservers.yaml       # Variables for webservers group
├── databases.yaml        # Variables for databases group
├── production.yaml       # Variables for production environment
└── development.yaml      # Variables for development environment
```

## Usage

Variables defined in these files automatically apply to hosts based on their group membership in your inventory.

### Example: `all.yaml`
Variables that apply to every host:

```yaml
---
# Common variables for all hosts
timezone: "Europe/Berlin"
ntp_servers:
  - "pool.ntp.org"
  - "time.cloudflare.com"

# Default feature flags
install_monitoring: true
install_security_updates: true

# Common packages
common_packages:
  - curl
  - wget
  - vim
  - htop
  - git
```

### Example: `webservers.yaml`
Variables specific to web servers:

```yaml
---
# Web server configuration
nginx_worker_processes: auto
nginx_worker_connections: 1024

# SSL configuration
ssl_cert_path: "/etc/ssl/certs"
ssl_key_path: "/etc/ssl/private"

# Firewall rules for web servers
firewall_rules:
  - { port: 80, protocol: tcp, source: "0.0.0.0/0" }
  - { port: 443, protocol: tcp, source: "0.0.0.0/0" }
```

### Example: `production.yaml`
Environment-specific variables:

```yaml
---
# Production environment settings
environment: production
log_level: warn
debug_mode: false

# Backup configuration
backup_retention_days: 30
backup_schedule: "0 2 * * *"

# Resource limits
max_connections: 1000
memory_limit: "2G"
```

## Variable Precedence

Ansible applies variables in this order (highest precedence first):

1. Extra vars (`-e` command line)
2. Host vars (`host_vars/`)
3. Group vars (`group_vars/`)
4. Role defaults
5. Inventory variables

## Best Practices

1. **Use descriptive names** - Make variable purposes clear
2. **Document complex variables** - Add comments explaining usage
3. **Group related variables** - Organize logically within files
4. **Use environment-specific files** - Separate prod/dev/staging configs
5. **Avoid secrets** - Use 1Password lookups or Ansible Vault instead

## Security Considerations

- **Never commit plain-text secrets** to group_vars files
- Use `community.general.onepassword` lookups for sensitive data:
  ```yaml
  database_password: "{{ lookup('community.general.onepassword', 'DB Password', field='password', vault='CI') }}"
  ```
- Consider using Ansible Vault for environment-specific secrets:
  ```bash
  ansible-vault encrypt group_vars/production.yaml
  ```

## Examples from This Project

Common patterns used in this infrastructure:

```yaml
---
# Feature toggles
docker_enabled: "{{ docker | default(true) }}"
tailscale_enabled: "{{ tailscale | default(true) }}"
backrest_enabled: "{{ backrest | default(true) }}"

# Network configuration
dns_servers:
  - "1.1.1.1"
  - "8.8.8.8"

# 1Password lookups
ssh_public_key: "{{ lookup('community.general.onepassword', 'MBA SSH - Public Key', field='public_key', vault='CI') }}"
tailscale_auth_key: "{{ lookup('community.general.onepassword', 'Tailscale Auth Key', field='key', vault='CI') }}"
```
