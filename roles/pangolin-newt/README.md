# Pangolin Newt Role

This role installs and configures Newt on a Debian-based server.

## Features

- Installation and configuration of the Newt tool via an update/installation script
- Setup of the Newt service for automatic execution
- Provision of a daily update script for Newt
- Reuse of `/etc/newt/newt.env`, including a configuration restored from backup,
  without requesting new credentials or creating a Pangolin site
- Migration of credentials from a legacy `newt.service` command line
- An incomplete existing configuration fails instead of creating a new identity

## Backup and restore

Include `/etc/newt/newt.env` in the encrypted backup alongside `/srv`.
Restore it with root ownership and mode `0600` **before** running the setup role.
Only a host without an existing configuration or legacy service creates a new
Pangolin site. Existing installations do not need Pangolin API credentials for
this role. The installer still maintains the binary, service unit and update job.

For host Tailscale, include `/var/lib/tailscale` in the backup. Restore that
directory before running any server setup playbook. The shared Tailscale setup
starts the daemon without `tailscale up` or a new auth key when a nonempty
`tailscaled.state` exists, preserving device identity and preferences. A deleted,
expired or revoked device may still require deliberate reauthentication; setup
does not silently replace it. Do not run the original and restored host with the
same identity simultaneously. Initramfs Tailscale remains a separate boot identity.

## Variables

| Variable | Default Value | Description |
|----------|---------------|-------------|
| install_newt | False | Enables the installation and configuration of Newt |
| newt_client_id | "" | The ID for the Newt client |
| newt_client_secret | "" | The secret for the Newt client |
| newt_client_endpoint | "https://connect.nwt.today" | The endpoint for the Newt client |

## Example

```yaml
- hosts: all
  roles:
    - role: pangolin-newt
      install_newt: True
      newt_client_id: "my-newt-id"
      newt_client_secret: "my-newt-secret"
      newt_client_endpoint: "https://connect.nwt.today"
```

## Dependencies

This role has no external dependencies but requires internet access to download Newt.

## Notes

- Newt is only installed and configured when the `install_newt` variable is set to `True`.
- The role sets up both an update script and a systemd service for Newt.
- The update script is set up as a daily cron job to keep Newt current.
- For setting up the systemd service, the variables `newt_client_id`, `newt_client_secret` and `newt_client_endpoint` are required.
