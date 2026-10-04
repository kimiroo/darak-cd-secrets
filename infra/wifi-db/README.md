# wifi-db

Secrets for the Wi-Fi device registry (CNPG cluster `wifi-postgres`, database `wifi`).

| Secret | Keys | Must match |
|---|---|---|
| `wifi-owner` | `username: wifi`, `password` | nothing (only the schema Job uses it) |
| `wifi-radius` | `username: wifi_radius`, `password` | **`freeradius-db` in `infra/freeradius`** (same `username` and `password`) |

Values: `openssl rand -hex 24`. Write the password into both files, then `sops -e -i` each.

Order: encrypt both, add them to `kustomization.yaml` (already listed), add `- wifi-db` to `../kustomization.yaml`.
Changing the `wifi-radius` password later: change `freeradius-db` in the same commit, FreeRADIUS must restart.
