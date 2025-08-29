# Shell Configuration Role

Diese Rolle konfiguriert die Shell-Umgebung auf einem Debian-basierten Server.

## Funktionen

- ZSH als Standard-Shell setzen
- Oh-My-Zsh installieren und konfigurieren
- ZSH Theme anpassen
- Nützliche Aliases und Update-Einstellungen für ZSH konfigurieren

## Variablen

Diese Rolle verwendet aktuell keine konfigurierbaren Variablen. Das ZSH-Theme ist fest auf "maran" eingestellt.

## Beispiel

```yaml
- hosts: all
  roles:
    - role: shell-config
```

## Abhängigkeiten

Diese Rolle setzt voraus, dass ZSH auf dem System installiert ist. Es wird empfohlen, die `base-system` Rolle vor dieser Rolle auszuführen, da diese die benötigten Pakete (einschließlich ZSH) installiert.

## Hinweis

Nach der Ausführung dieser Rolle wird ZSH als Standard-Shell für den Root-Benutzer konfiguriert. Die Konfiguration umfasst:
- Installation von Oh-My-Zsh
- Einrichtung des "maran" Themes
- Hinzufügen eines praktischen `update`-Alias für Systemaktualisierungen
- Konfiguration von automatischen Oh-My-Zsh Updates