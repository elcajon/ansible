# Backup Tools Role

This role installs and configures various backup tools on a Debian-based server.

## Features

- Installation and configuration of Backrest (a powerful backup tool)
- Setup of Backrest service and wrapper scripts
- Installation and configuration of RCLONE for cloud storage integration

## Variables

| Variable | Default Value | Description |
|----------|---------------|-------------|
| install_backrest | False | Enables the installation and configuration of Backrest |
| rclone_config | "" | RCLONE configuration as string (written directly to configuration file) |
| backrest_server_name | "localhost" | The server name (used for Backrest configuration) |
| install_docker | False | Information whether Docker is installed (used for Backrest hooks) |

## Example

```yaml
- hosts: all
  roles:
    - role: backup-tools
      install_backrest: True
      backrest_server_name: "my-server"
      install_docker: True
      rclone_config: |
        [remote]
        type = s3
        provider = AWS
        access_key_id = your-access-key
        secret_access_key = your-secret-key
        region = eu-central-1
```

## Dependencies

This role has no external dependencies but requires internet access to download the tools.

## Notes

- Backrest is only installed when the `install_backrest` variable is set to `True`.
- For Backrest configuration, two GUID strings are automatically generated.
- The RCLONE configuration is only created when the `rclone_config` variable is defined and contains a value.