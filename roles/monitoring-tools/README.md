# Monitoring Tools Role

This role installs and configures various monitoring tools on a Debian-based server.

## Features

- Configuration of HTop for improved system monitoring

## Variables

This role currently does not use any configurable variables.

## Example

```yaml
- hosts: all
  roles:
    - role: monitoring-tools
```

## Dependencies

This role assumes that HTop is installed on the system. It is recommended to run the `base-system` role before this role, as it installs HTop.

## Notes

- The role configures HTop with an optimized configuration for improved system monitoring.
- A configuration directory for HTop is created and a preconfigured HTop configuration is provided.