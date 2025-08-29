# Proxmox Setup Role

Diese Rolle führt spezifische Konfigurationen für einen Proxmox-Server durch.

## Funktionen

- Aktiviert IPv6 (deaktiviert die IPv6-Deaktivierung)
- Lädt nützliche Community-Skripte für Proxmox herunter:
  - Home Assistant OS VM Erstellungsskript
  - Post-Proxmox-Installation-Optimierungsskript

## Variablen

Diese Rolle verwendet aktuell keine konfigurierbaren Variablen.

## Beispiel

```yaml
- hosts: proxmox_hosts
  roles:
    - role: proxmox-setup
```

## Abhängigkeiten

Diese Rolle hat keine externen Abhängigkeiten.

## Hinweis

Die heruntergeladenen Skripte stammen aus dem Community-Scripts Repository für Proxmox und ermöglichen:

1. `ha-install.sh`: Einfache Installation von Home Assistant OS als VM
2. `post-pve-install.sh`: Optimierung einer frischen Proxmox-Installation

Die Skripte werden nach `/root/` heruntergeladen und mit Ausführungsrechten versehen, müssen aber manuell ausgeführt werden.