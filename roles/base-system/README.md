# Base System Role

This role performs basic system configurations on a Debian-based server.

## Features

- Update and clean the system
- Install required base packages
- Uninstall unnecessary packages
- Set timezone
- Clean up various files
- Empty /tmp directory

## Variables

| Variable | Default Value | Description |
|----------|---------------|-------------|
| system_timezone | Europe/Berlin | The timezone to be set |

## Example

```yaml
- hosts: all
  roles:
    - role: base-system
      system_timezone: "Europe/Vienna"
```

## Dependencies

This role has no external dependencies.