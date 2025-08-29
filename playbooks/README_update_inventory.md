ansible/update_inventory.md
# Inventory Update and Base System Update

This playbook (`update_inventory.yaml`) is used to update the base configuration on the local host as well as all remote hosts in the Tailscale network that are tagged with "ansible".

## Features

- Updates the base system configuration on the local host
- Updates the base system configuration on all remote hosts in the Tailscale network
- Uses dynamic Tailscale inventory for host discovery
- Skips timezone configuration on remote hosts

## Requirements

- Working Tailscale setup
- Local host with Ansible installed
- Remote hosts in the Tailscale network tagged with "ansible"
- SSH access to remote hosts

## Structure

The playbook consists of two main parts:

1. **Local Host Update**:
   - Runs the `base-system` role on the local host
   - Uses local connection without sudo

2. **Remote Hosts Update**:
   - Runs the `base-system` role on all hosts in the Tailscale network tagged with "ansible"
   - Uses sudo for execution
   - Skips timezone configuration

## Usage

```bash
ansible-playbook update_inventory.yaml
```

## Included Roles

This playbook uses the following role:

- `base-system`: Basic system configuration (updates, essential packages)

## Notes

- Timezone configuration is skipped on remote hosts to avoid unwanted changes
- The playbook uses Python 3 on remote hosts
- No sudo is required for the local host
- Sudo is used for remote hosts