# Komodo Periphery Role

Installs and configures the [Komodo](https://komo.do) Periphery agent **directly
as a systemd service** (instead of in a container), analogous to the
`pangolin-newt` role. Targets Komodo v2 (PKI onboarding).

## How it works

- On **first install** (`tasks/komodo_api.yml`), an onboarding key is requested
  from Komodo Core via the admin API (`POST /write/CreateOnboardingKey`) and
  written into `periphery.config.toml`. On first connect Periphery generates its
  own Ed25519 keypair (`{{ periphery_root_directory }}/keys/periphery.key`) and
  sends only its public key to Core. The onboarding key is then removed from the
  config (no long-lived secret is stored).
- The binary is kept current by a daily updater (`/etc/cron.daily/periphery_update`)
  tracking the latest `moghtech/komodo` release (asset `periphery-{x86_64,aarch64}`).
  No version pinning.
- Periphery runs in **outbound mode**: it dials `core_address`; Core does not need
  to reach the agent's inbound port.

## Setup vs. update

- **Setup** (`apply-server-roles.yaml`): installed on every host where Docker is
  installed (`install_periphery: "{{ docker | bool }}"`).
- **Update** (`update-inventory.yaml` → `tasks/update.yml`): if Docker is present,
  Periphery is updated to the latest version — or installed, for hosts that have
  Docker but not yet Periphery (transition cases).

## Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `install_periphery` | `false` | Install/configure Periphery on this host. |
| `komodo_core_address` | `http://100.90.80.191:8080` | Address Periphery dials (outbound mode). Core's **Tailscale IP** — DNS-independent and avoids the `/etc/hosts` short-name trap on the Core host itself (where `pangolin` resolves to `127.0.1.1`). Fallback on IP change: the FQDN `http://pangolin.stern-chimera.ts.net:8080` (never the short name). |
| `komodo_api_url` | `{{ komodo_core_address }}` | Komodo Core API endpoint for `CreateOnboardingKey`. Must be the **internal** Core address — the public UI URL (`op://CI/Komodo/URL`, `https://komodo.max-venz.io`) sits behind the Pangolin auth proxy and rejects API calls with HTTP 302. |
| `komodo_api_key` / `komodo_api_secret` | `""` | Admin API credentials. Inject from `op://CI/Komodo/{Key,Secret}`. |
| `periphery_connect_as` | `{{ server_name \| default(inventory_hostname) }}` | Server name registered in Komodo. |
| `periphery_root_directory` | `/srv/komodo-periphery` | Root dir (mirrors compose `PERIPHERY_ROOT_DIRECTORY`). |
| `periphery_stack_dir` | `/srv` | Stack dir (mirrors compose `PERIPHERY_STACK_DIR`). |
| `periphery_include_disk_mounts` | `[/etc/hostname]` | Disk-report whitelist (mirrors compose). |
| `periphery_disable_terminals` | `false` | Disable remote shell access. |

## Notes

- Both `komodo_core_address` (agent connection) and `komodo_api_url` (admin API
  call) use the **internal** Core address (Core's Tailscale IP `100.90.80.191:8080`).
  The public UI URL `https://komodo.max-venz.io` is behind the Pangolin auth proxy
  and cannot be used for API calls (returns HTTP 302 to a login page).
- `connect_as` must match the server name registered in Komodo. When migrating a
  host from a container-based Periphery, the existing server (and its registered
  public key) keep their name — either rename the Komodo server to the host's
  name, or override `periphery_connect_as` for that host.
