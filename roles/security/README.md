# Security Role

This role implements basic security measures on a Debian-based server.

## Features

- SSH hardening and security
- Optional: Disable SSH service (for systems that should only be accessible via Tailscale)
- Enable IPv4 & IPv6 forwarding
- Configuration of unattended-upgrades for automatic security updates

## Variables

| Variable | Default Value | Description |
|----------|---------------|-------------|
| disable_ssh_for_tailscale | False | If True, the SSH service will be disabled (for systems that should only be accessible via Tailscale) |

## Example

```yaml
- hosts: all
  roles:
    - role: security
      disable_ssh_for_tailscale: True
```

## Dependencies

This role has no external dependencies but assumes that the corresponding SSH services are installed on the system.

## Notes

IPv4 & IPv6 forwarding is included in this role because it affects security-related network configurations and is often used in conjunction with firewalls and VPN solutions.