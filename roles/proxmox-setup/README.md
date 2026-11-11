# Proxmox Setup Role

This role performs specific configurations for a Proxmox server.

## Features

- Enables IPv6 (disables the IPv6 deactivation)
- Downloads useful community scripts for Proxmox:
  - Home Assistant OS VM creation script
  - Post-Proxmox-installation optimization script
- Sets the CPU frequency governor and energy performance preference persistently
  via a `cpu-governor.service` systemd unit (Proxmox defaults to `performance`,
  which keeps mobile CPUs at full turbo while idle and makes fans loud)

## Variables

| Variable | Default | Description |
|---|---|---|
| `proxmox_cpu_governor` | `powersave` | CPU frequency governor (`performance` or `powersave` with intel_pstate) |
| `proxmox_cpu_epp` | `balance_power` | Energy performance preference (`default`, `performance`, `balance_performance`, `balance_power`, `power`) |

## Example

```yaml
- hosts: proxmox_hosts
  roles:
    - role: proxmox-setup
```

## Dependencies

This role has no external dependencies.

## Notes

The downloaded scripts come from the Community Scripts repository for Proxmox and enable:

1. `ha-install.sh`: Easy installation of Home Assistant OS as a VM
2. `post-pve-install.sh`: Optimization of a fresh Proxmox installation

The scripts are downloaded to `/root/` and provided with execution rights, but must be executed manually.