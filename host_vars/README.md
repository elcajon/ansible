# Host Variables

This directory contains variable files that apply to specific hosts defined in your inventory.

## Structure

Host variable files should be named after the individual hosts they apply to:

```
host_vars/
├── webserver01.example.com.yaml    # Variables for specific host
├── db-primary.yaml                 # Variables for database primary
├── 192.168.1.100.yaml             # Variables for host by IP
└── docker-host/                    # Directory for complex host config
    ├── main.yaml
    ├── secrets.yaml
    └── network.yaml
```

## Usage

Variables defined in these files automatically apply to the specific host they're named after, regardless of group membership.

### Example: `webserver01.example.com.yaml`
Host-specific configuration:

```yaml
---
# Host-specific network configuration
ansible_host: 10.0.0.10
ansible_port: 2222
ansible_user: deploy

# Custom resource allocation
nginx_worker_processes: 4
max_memory_usage: "4G"

# Host-specific SSL certificate
ssl_certificate_name: "webserver01.example.com"

# Unique identifiers
server_id: "web-001"
datacenter: "eu-west-1"
```

### Example: `db-primary.yaml`
Database server configuration:

```yaml
---
# Database-specific settings
mysql_server_id: 1
mysql_log_bin: true
mysql_max_connections: 500

# Replication configuration
mysql_replication_role: master
mysql_replication_user: replication

# Storage configuration
data_volume_size: "100G"
backup_volume_size: "200G"

# Performance tuning
innodb_buffer_pool_size: "2G"
query_cache_size: "256M"
```

### Example: `192.168.1.100.yaml`
Host identified by IP address:

```yaml
---
# Connection details
ansible_user: root
ansible_ssh_private_key_file: ~/.ssh/special_key

# Host-specific packages
additional_packages:
  - docker-ce
  - nvidia-docker2

# Custom firewall rules
firewall_rules:
  - { port: 8080, protocol: tcp, source: "192.168.1.0/24" }
  - { port: 3000, protocol: tcp, source: "10.0.0.0/8" }
```

## Directory Structure for Complex Hosts

For hosts with many variables, you can create a directory:

```
host_vars/
└── complex-server/
    ├── main.yaml         # Primary configuration
    ├── network.yaml      # Network-specific variables
    ├── storage.yaml      # Storage configuration
    └── applications.yaml # Application settings
```

### Example: `complex-server/main.yaml`
```yaml
---
# Basic host information
ansible_host: 10.0.0.50
ansible_user: admin
environment: production

# Resource limits
cpu_limit: 8
memory_limit: "16G"
disk_space: "500G"
```

### Example: `complex-server/network.yaml`
```yaml
---
# Network interfaces
network_interfaces:
  eth0:
    ip: "10.0.0.50/24"
    gateway: "10.0.0.1"
  eth1:
    ip: "192.168.100.50/24"
    gateway: "192.168.100.1"

# DNS configuration
dns_nameservers:
  - "10.0.0.1"
  - "1.1.1.1"

# VPN configuration
tailscale_hostname: "complex-server"
tailscale_tags:
  - "server"
  - "production"
```

## Variable Precedence

Host variables have higher precedence than group variables:

1. **Highest**: Extra vars (`-e` command line)
2. **Host vars** (`host_vars/`) ← **These files**
3. Group vars (`group_vars/`)
4. Role defaults
5. **Lowest**: Inventory variables

## Common Use Cases

### Connection Parameters
```yaml
---
# Custom SSH configuration
ansible_host: internal-ip.example.com
ansible_port: 2222
ansible_user: service-account
ansible_ssh_private_key_file: ~/.ssh/service_key
ansible_ssh_common_args: '-o ProxyJump=bastion.example.com'
```

### Host-Specific Features
```yaml
---
# Feature flags for this specific host
install_docker: true
install_kubernetes: false
enable_monitoring: true
enable_backup: false

# Host role identification
server_role: "application"
server_tier: "frontend"
```

### Resource Configuration
```yaml
---
# Hardware-specific tuning
cpu_cores: 8
memory_gb: 32
storage_type: "ssd"

# Application sizing
java_heap_size: "8G"
nginx_worker_processes: 8
database_buffer_pool: "16G"
```

### Environment-Specific Settings
```yaml
---
# Environment identification
environment: staging
debug_enabled: true
log_level: debug

# External service endpoints
api_endpoint: "https://staging-api.example.com"
database_host: "staging-db.internal"
cache_host: "staging-redis.internal"
```

## Security Best Practices

1. **Avoid plain-text secrets** - Use 1Password lookups:
   ```yaml
   database_password: "{{ lookup('community.general.onepassword', 'DB Password', field='password', vault='CI') }}"
   ```

2. **Use Ansible Vault** for sensitive host-specific data:
   ```bash
   ansible-vault encrypt host_vars/sensitive-server.yaml
   ```

3. **Limit file permissions**:
   ```bash
   chmod 600 host_vars/*.yaml
   ```

4. **Use separate files** for secrets:
   ```
   host_vars/
   └── webserver01/
       ├── main.yaml      # Non-sensitive config
       └── vault.yaml     # Encrypted secrets
   ```

## Integration with This Project

Common patterns for hosts in this infrastructure:

```yaml
---
# Tailscale integration
tailscale_hostname: "{{ inventory_hostname }}"
tailscale_auth_key: "{{ lookup('community.general.onepassword', 'Tailscale Auth Key', field='key', vault='CI') }}"

# SSH key configuration
authorized_ssh_keys:
  - "{{ lookup('community.general.onepassword', 'MBA SSH - Public Key', field='public_key', vault='CI') }}"

# Backup configuration
backrest_repo_host: "backup.tailscale.network"
backrest_repo_path: "/backups/{{ inventory_hostname }}"

# Container configuration
docker_daemon_options:
  - "--log-driver=journald"
  - "--storage-driver=overlay2"
```

## Troubleshooting

- **Variables not applying**: Check filename matches inventory hostname exactly
- **Connection issues**: Verify `ansible_host`, `ansible_user`, and SSH key settings
- **Permission errors**: Ensure SSH keys and file permissions are correct
- **Variable conflicts**: Remember host_vars override group_vars

For more examples, see the actual playbooks in `playbooks/` that demonstrate these patterns.