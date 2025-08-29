# Backup Tools Role

Diese Rolle installiert und konfiguriert verschiedene Backup-Tools auf einem Debian-basierten Server.

## Funktionen

- Installation und Konfiguration von Backrest (ein leistungsfähiges Backup-Tool)
- Einrichtung des Backrest-Service und Wrapper-Skripts
- Installation und Konfiguration von RCLONE für Cloud-Speicher-Integration

## Variablen

| Variable | Standardwert | Beschreibung |
|----------|--------------|--------------|
| install_backrest | False | Aktiviert die Installation und Konfiguration von Backrest |
| rclone_config | "" | RCLONE-Konfiguration als String (wird direkt in die Konfigurationsdatei geschrieben) |
| backrest_server_name | "localhost" | Der Name des Servers (wird für Backrest-Konfiguration verwendet) |
| install_docker | False | Information, ob Docker installiert ist (wird für Backrest-Hooks verwendet) |

## Beispiel

```yaml
- hosts: all
  roles:
    - role: backup-tools
      install_backrest: True
      backrest_server_name: "mein-server"
      install_docker: True
      rclone_config: |
        [remote]
        type = s3
        provider = AWS
        access_key_id = your-access-key
        secret_access_key = your-secret-key
        region = eu-central-1
```

## Abhängigkeiten

Diese Rolle hat keine externen Abhängigkeiten, benötigt aber Internetzugang, um die Tools herunterzuladen.

## Hinweis

- Backrest wird nur installiert, wenn die Variable `install_backrest` auf `True` gesetzt ist.
- Für die Backrest-Konfiguration werden automatisch zwei GUID-Strings generiert.
- Die RCLONE-Konfiguration wird nur erstellt, wenn die Variable `rclone_config` definiert ist und einen Wert enthält.