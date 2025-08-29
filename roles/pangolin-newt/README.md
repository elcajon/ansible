# Pangolin Newt Role

This role installs and configures Newt on a Debian-based server.

## Features

- Installation and configuration of the Newt tool via an update/installation script
- Setup of the Newt service for automatic execution
- Provision of a daily update script for Newt

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