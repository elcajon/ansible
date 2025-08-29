# Security Role

Diese Rolle implementiert grundlegende Sicherheitsmaßnahmen auf einem Debian-basierten Server.

## Funktionen

- SSH Hardening und Absicherung
- Optional: SSH-Service deaktivieren (für Systeme, die nur über Tailscale erreichbar sein sollen)
- IPv4 & IPv6 Forwarding aktivieren
- Konfiguration von unattended-upgrades für automatische Sicherheitsupdates

## Variablen

| Variable | Standardwert | Beschreibung |
|----------|--------------|--------------|
| disable_ssh_for_tailscale | False | Wenn True, wird der SSH-Dienst deaktiviert (für Systeme, die nur über Tailscale erreichbar sein sollen) |

## Beispiel

```yaml
- hosts: all
  roles:
    - role: security
      disable_ssh_for_tailscale: True
```

## Abhängigkeiten

Diese Rolle hat keine externen Abhängigkeiten, setzt aber voraus, dass die entsprechenden SSH-Dienste auf dem System installiert sind.

## Hinweis

IPv4 & IPv6 Forwarding ist in dieser Rolle enthalten, da es sicherheitsrelevante Netzwerkkonfigurationen betrifft und oft in Verbindung mit Firewalls und VPN-Lösungen verwendet wird.