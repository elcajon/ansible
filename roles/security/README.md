# Security Role

This role implements basic security measures on a Debian-based server.

## Features

- SSH hardening via a drop-in file (`/etc/ssh/sshd_config.d/99-hardening.conf`)
  so it cannot be overridden by cloud-init or distribution drop-ins. The
  effective configuration is validated with `sshd -t` before SSH is restarted,
  and SSH is only restarted when the configuration actually changed.
- Optional: Disable (and mask) the SSH service for systems that should only be
  accessible via Tailscale. Masking ensures the service stays down even after an
  `openssh-server` package update would otherwise re-enable it. Access is
  expected via Tailscale SSH.
- Enable IPv4 & IPv6 forwarding via a persistent sysctl drop-in
  (`/etc/sysctl.d/99-ip-forward.conf`), e.g. for Tailscale exit nodes. Managed as
  a file (not set live) so it stays idempotent even in LXC containers, where the
  live value resets to 0 on reboot.
- Configuration of unattended-upgrades for automatic security updates,
  with automatic reboots disabled by default
- Maintenance checks: ensures time synchronization (systemd-timesyncd) is
  active and reports a pending reboot in the play output

## Variables

| Variable | Default Value | Description |
|----------|---------------|-------------|
| security_disable_ssh_for_tailscale | `False` | If `True`, the SSH service is disabled (for hosts that should only be reachable via Tailscale). |
| security_ssh_hardening_options | see [defaults/main.yml](defaults/main.yml) | List of directives written to the SSH hardening drop-in. `PermitRootLogin prohibit-password` is kept because Ansible connects as root via SSH key. |
| security_unattended_automatic_reboot | `False` | Updates install automatically, but required reboots are performed manually. Keep disabled on LUKS hosts that need operator-assisted unlock. |
| security_unattended_automatic_reboot_time | `"08:00"` | Only used if automatic reboots are explicitly enabled. |

## Example

```yaml
- hosts: all
  roles:
    - role: security
      security_disable_ssh_for_tailscale: True
```

Disable the automatic reboot on a specific host (e.g. a Proxmox hypervisor) via
`host_vars`:

```yaml
# host_vars/proxmox.yml
security_unattended_automatic_reboot: false
```

The `update-inventory` playbook checks `/run/reboot-required` again after all
update tasks and prints a final `NEUSTART ERFORDERLICH` notice for each affected
host. This is a notice in the Ansible output; no mail or push service is needed.
Applying `false` does not cancel a reboot already scheduled by an earlier run;
check `shutdown --show` and cancel an update-scheduled reboot separately.

## Dependencies

This role has no external dependencies but assumes that the corresponding SSH
services are installed on the system.

## Notes

- IPv4 & IPv6 forwarding is included in this role because it affects
  security-related network configurations and is often used in conjunction with
  firewalls and VPN solutions.
- This role does **not** manage a host firewall. Firewalling is handled outside
  Ansible: a Hetzner Cloud Firewall for the public host, and the Proxmox VE
  firewall for the local hosts.
