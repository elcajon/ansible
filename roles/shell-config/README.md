# Shell Configuration Role

This role configures the shell environment on a Debian-based server.

## Features

- Set ZSH as the default shell
- Install and configure Oh-My-Zsh
- Customize ZSH theme
- Configure useful aliases and update settings for ZSH

## Variables

This role currently does not use any configurable variables. The ZSH theme is fixed to "maran".

## Example

```yaml
- hosts: all
  roles:
    - role: shell-config
```

## Dependencies

This role assumes that ZSH is installed on the system. It is recommended to run the `base-system` role before this role, as it installs the required packages (including ZSH).

## Notes

After executing this role, ZSH will be configured as the default shell for the root user. The configuration includes:
- Installation of Oh-My-Zsh
- Setup of the "maran" theme
- Adding a convenient `update` alias for system updates
- Configuration of automatic Oh-My-Zsh updates