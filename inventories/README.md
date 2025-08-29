# Ansible Inventar

Dieses Verzeichnis enthält ein dynamisches Inventar-Skript für Ansible, das Hosts im Tailscale-Netzwerk automatisch erkennt und gruppiert.

## `tailscale.py`

Dieses Python-Skript ist ein dynamisches Inventar für Ansible, das mit Tailscale-Netzwerken funktioniert. Es bietet folgende Funktionen:

- Automatische Erkennung aller Nodes im Tailscale-Netzwerk
- Gruppierung von Hosts basierend auf:
  - Online-Status (`online` und `offline` Gruppen)
  - Betriebssystem (`Linux`, `Darwin`, etc.)
  - Tailscale-Tags (mit Anpassung für Ansible-Kompatibilität)
- Automatische Bereitstellung von SSH-Hostnamen über Tailscale DNS
- Konfiguration von `root` als Standard-Benutzer für SSH-Verbindungen

### Voraussetzungen

- Tailscale muss auf dem lokalen System installiert und ausgeführt werden
- Python 3.6 oder höher
- Ausführungsrechte für das Skript (`chmod +x tailscale.py`)

### Verwendung

Das Skript kann direkt mit Ansible verwendet werden:

```bash
ansible -i inventories/tailscale.py all -m ping
```

Oder in einem Playbook:

```yaml
---
- name: Beispiel-Playbook
  hosts: tag_ansible
  gather_facts: true
  tasks:
    - name: Ping
      ping:
```

Dieses Beispiel würde alle Hosts im Tailscale-Netzwerk ansprechen, die den Tag "ansible" haben.

### Gruppen

Das Skript erstellt automatisch die folgenden Gruppen:

- `all`: Alle Hosts im Tailscale-Netzwerk
- `online`: Alle Hosts, die aktuell online sind
- `offline`: Alle Hosts, die aktuell offline sind
- Betriebssystem-Gruppen: `Linux`, `Darwin`, etc.
- Tag-basierte Gruppen: Für jeden Tailscale-Tag wird eine Gruppe erstellt (`:` und `-` werden durch `_` ersetzt)

### Hinweise

- Das Skript ignoriert den speziellen Host "funnel-ingress-node"
- Hosts ohne OS-Information werden ignoriert (z.B. Mullvad Exit-Nodes)
- Hosts ohne SSH-Hostkeys werden ignoriert
- Der lokale Host wird automatisch in die entsprechenden Gruppen eingefügt