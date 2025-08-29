# Base System Role

Diese Rolle führt grundlegende Systemkonfigurationen auf einem Debian-basierten Server durch.

## Funktionen

- System aktualisieren und bereinigen
- Benötigte Basispakete installieren
- Nicht benötigte Pakete deinstallieren
- Zeitzone setzen
- Aufräumen diverser Dateien
- /tmp Verzeichnis leeren

## Variablen

| Variable | Standardwert | Beschreibung |
|----------|--------------|--------------|
| system_timezone | Europe/Berlin | Die zu setzende Zeitzone |

## Beispiel

```yaml
- hosts: all
  roles:
    - role: base-system
      system_timezone: "Europe/Vienna"
```

## Abhängigkeiten

Diese Rolle hat keine externen Abhängigkeiten.