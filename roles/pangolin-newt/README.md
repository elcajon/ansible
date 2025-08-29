# Newt Role

Diese Rolle installiert und konfiguriert Newt auf einem Debian-basierten Server.

## Funktionen

- Installation und Konfiguration des Newt-Tools über ein Update/Installations-Skript
- Einrichtung des Newt-Service für automatische Ausführung
- Bereitstellung eines täglichen Update-Skripts für Newt

## Variablen

| Variable | Standardwert | Beschreibung |
|----------|--------------|--------------|
| install_newt | False | Aktiviert die Installation und Konfiguration von Newt |
| newt_client_id | "" | Die ID für den Newt-Client |
| newt_client_secret | "" | Das Secret für den Newt-Client |
| newt_client_endpoint | "https://connect.nwt.today" | Der Endpoint für den Newt-Client |

## Beispiel

```yaml
- hosts: all
  roles:
    - role: newt
      install_newt: True
      newt_client_id: "meine-newt-id"
      newt_client_secret: "mein-newt-secret"
      newt_client_endpoint: "https://connect.nwt.today"
```

## Abhängigkeiten

Diese Rolle hat keine externen Abhängigkeiten, benötigt aber Internetzugang, um Newt herunterzuladen.

## Hinweis

- Newt wird nur installiert und konfiguriert, wenn die Variable `install_newt` auf `True` gesetzt ist.
- Die Rolle richtet sowohl ein Update-Skript als auch einen Systemd-Service für Newt ein.
- Das Update-Skript wird als täglicher Cron-Job eingerichtet, um Newt aktuell zu halten.
- Für die Einrichtung des Systemd-Services werden die Variablen `newt_client_id`, `newt_client_secret` und `newt_client_endpoint` benötigt.