# Backup Tools Role

This role installs and configures RCLONE for cloud storage integration on a Debian-based server.

## Variables

| Variable | Default Value | Description |
|----------|---------------|-------------|
| rclone_config | "" | RCLONE configuration as string (written directly to configuration file) |

## Example

```yaml
- hosts: all
  roles:
    - role: backup-tools
      rclone_config: |
        [remote]
        type = s3
        provider = AWS
        access_key_id = your-access-key
        secret_access_key = your-secret-key
        region = eu-central-1
```

## Dependencies

This role has no external dependencies but requires internet access to download RCLONE.

## Notes

- The RCLONE configuration is only created when the `rclone_config` variable is defined and contains a value.
