# Monitoring Tools Role

Diese Rolle installiert und konfiguriert verschiedene Monitoring-Tools auf einem Debian-basierten Server.

## Funktionen

- Konfiguration von HTop für verbesserte Systemüberwachung

## Variablen

Diese Rolle verwendet aktuell keine konfigurierbaren Variablen.

## Beispiel

```yaml
- hosts: all
  roles:
    - role: monitoring-tools
```

## Abhängigkeiten

Diese Rolle setzt voraus, dass HTop auf dem System installiert ist. Es wird empfohlen, die `base-system` Rolle vor dieser Rolle auszuführen, da diese HTop installiert.

## Hinweis

- Die Rolle konfiguriert HTop mit einer optimierten Konfiguration für eine verbesserte Systemüberwachung.
- Es wird ein Konfigurationsverzeichnis für HTop erstellt und eine vorkonfigurierte HTop-Konfiguration bereitgestellt.