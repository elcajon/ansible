# Inventories

This directory contains inventory files and scripts for managing hosts in the Ansible infrastructure.

## Structure

```
inventories/
├── README.md          # This documentation
└── tailscale.py       # Dynamic inventory script for Tailscale network
```

## Dynamic Inventory

### Tailscale Inventory (`tailscale.py`)

The `tailscale.py` script provides dynamic inventory by querying the Tailscale network for connected devices.

#### Features

- Automatically discovers all devices in your Tailscale network
- Groups hosts by Tailscale tags, operating system, and status
- Provides host variables including IP addresses and device information
- Updates in real-time based on current network state
- Supports filtering by tags, users, and connection status

#### Usage

```bash
# List all hosts
ansible-inventory --list --inventory inventories/tailscale.py

# Use with playbooks
ansible-playbook playbooks/system/setup-generic-debian-by-ip.yaml \
  --inventory inventories/tailscale.py

# Target specific groups
ansible servers -m ping --inventory inventories/tailscale.py
```

#### Configuration

The script can be configured through environment variables:

```bash
export TAILSCALE_API_KEY="tskey-api-..."  # Tailscale API key
export TAILSCALE_TAILNET="example.com"    # Your tailnet name
export TAILSCALE_INCLUDE_OFFLINE=false    # Include offline devices
```

#### Generated Groups

The script automatically creates these groups:

- **Tag-based groups**: Each Tailscale tag becomes a group
  - `server`: Devices tagged as servers
  - `desktop`: Desktop/laptop devices
  - `mobile`: Mobile devices
  - Custom tags as configured in Tailscale

- **OS-based groups**:
  - `linux`: Linux-based devices
  - `windows`: Windows devices
  - `darwin`: macOS devices
  - `android`: Android devices
  - `ios`: iOS devices

- **Status groups**:
  - `online`: Currently connected devices
  - `offline`: Disconnected devices (if included)

#### Host Variables

Each host receives these variables automatically:

```yaml
ansible_host: "100.64.0.10"           # Tailscale IP for connection
tailscale_hostname: "web-server-01"    # Device hostname in Tailscale
tailscale_ip: "100.64.0.10"           # Tailscale network IP
public_ip: "203.0.113.10"             # Public IP (if available)
os: "linux"                           # Operating system
last_seen: "2024-01-15T10:30:00Z"     # Last connection timestamp
tags: ["server", "web"]               # Applied Tailscale tags
user: "admin@example.com"             # Device owner
```

## Static Inventories

You can also create static inventory files in this directory:

### Example: `production.yaml`

```yaml
---
all:
  children:
    webservers:
      hosts:
        web01.example.com:
          ansible_host: 10.0.0.10
        web02.example.com:
          ansible_host: 10.0.0.11
      vars:
        http_port: 80
        https_port: 443
    
    databases:
      hosts:
        db01.example.com:
          ansible_host: 10.0.0.20
          mysql_port: 3306
      vars:
        db_backup_hour: 2

    production:
      children:
        webservers:
        databases:
      vars:
        environment: production
        log_level: warn
```

### Example: `development.ini`

```ini
[webservers]
dev-web01 ansible_host=192.168.1.10
dev-web02 ansible_host=192.168.1.11

[databases]  
dev-db01 ansible_host=192.168.1.20

[development:children]
webservers
databases

[development:vars]
environment=development
log_level=debug
```

## Inventory Precedence

When multiple inventory sources are specified, Ansible merges them with this precedence:

1. **Command line** (`-i` option)
2. **ansible.cfg** configuration
3. **Environment variables** (`ANSIBLE_INVENTORY`)
4. **Default locations** (`./inventory`, `./hosts`)

## Using Multiple Inventories

You can combine static and dynamic inventories:

```bash
ansible-playbook playbooks/system/setup-generic-debian-by-ip.yaml \
  -i inventories/production.yaml \
  -i inventories/tailscale.py
```

## Inventory Plugins

This project uses these inventory plugins (configured in `ansible.cfg`):

- `host_list`: Simple comma-separated host lists
- `script`: Executable inventory scripts (like `tailscale.py`)
- `auto`: Automatic format detection
- `yaml`: YAML inventory files
- `ini`: INI-style inventory files

## Security Considerations

- **Protect inventory files** containing sensitive IP addresses or host information
- **Use vault encryption** for sensitive inventory variables:
  ```bash
  ansible-vault encrypt inventories/production.yaml
  ```
- **Limit API access** for dynamic inventory scripts
- **Regular audits** of inventory access and modifications

## Best Practices

1. **Organize by environment** (production, staging, development)
2. **Use descriptive group names** that reflect host functions
3. **Keep secrets in separate files** or use 1Password lookups
4. **Document custom groups** and their purposes
5. **Test inventory changes** before deploying to production
6. **Use dynamic inventory** for cloud and container environments
7. **Version control** static inventory files

## Troubleshooting

### Dynamic Inventory Issues

```bash
# Test dynamic inventory script directly
./inventories/tailscale.py --list

# Check for Python/dependency issues
python3 inventories/tailscale.py --list

# Verify API credentials
tailscale status
```

### Static Inventory Issues

```bash
# Validate inventory syntax
ansible-inventory --list --inventory inventories/production.yaml

# Test host connectivity
ansible all -m ping --inventory inventories/production.yaml

# Check group assignments
ansible-inventory --graph --inventory inventories/production.yaml
```

### Common Error Solutions

- **"No hosts matched"**: Check inventory file paths and group names
- **"Authentication failed"**: Verify SSH keys and user credentials  
- **"Script not executable"**: Ensure dynamic inventory scripts have execute permissions
- **"YAML syntax error"**: Validate YAML inventory file syntax

## Maintenance

### Regular Tasks

1. **Update dynamic inventory** to reflect current network state
2. **Review and clean up** unused static inventory entries
3. **Rotate API keys** used by dynamic inventory scripts
4. **Test connectivity** to all inventory hosts
5. **Update documentation** when adding new inventory sources

### Automation

Consider automating inventory maintenance:

```bash
# Update Tailscale inventory hourly
0 * * * * cd /path/to/ansible && ansible-playbook playbooks/system/update-inventory.yaml
```

## Integration

The inventory system integrates with:

- **Playbooks**: All playbooks can use any inventory source
- **Roles**: Variables from inventory are available in roles
- **Vault**: Encrypted inventory files and variables
- **Tags**: Both Ansible and Tailscale tags for host selection
- **Monitoring**: Inventory data can feed into monitoring systems

For updating inventory automatically, see `playbooks/system/update-inventory.yaml`.