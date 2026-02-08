# Ansible Infrastructure Scripts

Ansible Playbooks und Roles zur Automatisierung von Server-Setup und -Konfiguration.

## Voraussetzungen

- Ansible 2.16+, Python 3.8+
- [1Password CLI](https://developer.1password.com/docs/cli/) (`op`) konfiguriert
- SSH-Zugang zu den Zielsystemen

## Setup

```bash
ansible-galaxy collection install -r requirements.yaml
```

## Playbooks

```bash
# Hetzner Cloud Server erstellen
ansible-playbook playbooks/infrastructure/create-hetzner.yaml

# Proxmox LXC Container erstellen
ansible-playbook playbooks/infrastructure/create-debian-lxc-pve.yaml

# Bestehendes Debian/Ubuntu System konfigurieren
ansible-playbook playbooks/system/setup-generic-debian-by-ip.yaml

# Inventar über Tailscale aktualisieren
ansible-playbook playbooks/system/update-inventory.yaml
```

## Roles

| Role | Beschreibung |
|------|-------------|
| `base-system` | Grundlegende Systemkonfiguration |
| `security` | SSH, Firewall, Fail2ban |
| `shell-config` | ZSH mit Oh-My-Zsh |
| `container-tools` | Docker |
| `backup-tools` | Backrest Backup mit S3 |
| `monitoring-tools` | System-Monitoring |
| `pangolin-newt` | Pangolin Newt Reverse-Proxy |
| `proxmox-setup` | Proxmox-Host Konfiguration |

## Inventar

Dynamisches Inventar via Tailscale (`inventories/tailscale.py`).

## 1Password

Alle Secrets werden über `community.general.onepassword` aus dem Tresor **CI** geladen. Folgende Einträge müssen vorhanden sein:

| Eintrag | Felder |
|---------|--------|
| `Hetzner Cloud API` | `token` |
| `Proxmox API` | `Host-Name`, `Benutzername`, `TokenID`, `Anmeldedaten` |
| `Tailscale Token` | `Anmeldedaten` |
| `Tailscale Token - Remote Server` | `Anmeldedaten` |
| `Pangolin - Mannheim` | `Anmeldedaten`, `Host-Name`, `Organisation` |
| `MBA SSH - Public Key` | `Benutzername` |
| `Backrest Hetzner` | `Anmeldedaten` |
| `S3 - Hetzner` | `Access Key`, `Secret Key` |
| `Rclone Config` | *(Dokument, geladen via `onepassword_doc`)* |

## Lizenz

MIT
