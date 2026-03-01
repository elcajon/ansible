# Container Tools Role

This role installs and configures container tools on a Debian-based server.

## Features

- Installation of Docker via the official install script
- Installation of [ctop](https://github.com/bcicen/ctop) (top-like interface for container metrics)

## Variables

| Variable | Default Value | Description |
|----------|---------------|-------------|
| install_docker | False | Enables the installation of Docker and ctop |

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
- ctop is automatically installed alongside Docker and placed in `/usr/local/bin/ctop`.
- The latest ctop release is fetched dynamically from the GitHub API during installation.