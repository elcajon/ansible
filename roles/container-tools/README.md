# Container Tools Role

Diese Rolle installiert und konfiguriert Container-Tools auf einem Debian-basierten Server.

## Funktionen

- Installation von Docker über das offizielle Install-Skript

## Variablen

| Variable | Standardwert | Beschreibung |
|----------|--------------|--------------|
| install_docker | False | Aktiviert die Installation von Docker |

## Beispiel

```yaml
- hosts: all
  roles:
    - role: container-tools
      install_docker: True
```

## Abhängigkeiten

Diese Rolle hat keine externen Abhängigkeiten, benötigt aber Internetzugang, um Docker herunterzuladen.

## Hinweis

- Docker wird nur installiert, wenn die Variable `install_docker` auf `True` gesetzt ist.
- Die Installation erfolgt über das offizielle Docker Install-Skript.
- Nach der Installation können Docker-Container ohne zusätzliche Konfiguration verwendet werden.