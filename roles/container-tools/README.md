# Container Tools Role

This role installs and configures container tools on a Debian-based server.

## Features

- Installation of Docker via the official install script

## Variables

| Variable | Default Value | Description |
|----------|---------------|-------------|
| install_docker | False | Enables the installation of Docker |

## Example

```yaml
- hosts: all
  roles:
    - role: container-tools
      install_docker: True
```

## Dependencies

This role has no external dependencies but requires internet access to download Docker.

## Notes

- Docker is only installed when the `install_docker` variable is set to `True`.
- Installation is done via the official Docker install script.
- After installation, Docker containers can be used without additional configuration.