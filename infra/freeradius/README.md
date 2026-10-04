# freeradius

| Secret | Keys | Must match / comes from |
|---|---|---|
| `freeradius` | `openwrt_secret` only | the shared secret entered in every OpenWrt AP (RADIUS secret) |
| `freeradius-db` | `username: wifi_radius`, `password` | **`wifi-radius` in `infra/wifi-db`** |
| `freeradius-ldap-wifi` | `password` | **`authentik-ldap-wifi-search` (`token`) in `infra/authentik`**, same value |
| `authentik-ldap-wifi-outpost` | `token` | issued by authentik, see below |

Remove the obsolete `authentik_secret` key from `freeradius.secret.yaml` (`sops freeradius.secret.yaml`).

## Getting `authentik-ldap-wifi-outpost` (AUTHENTIK_TOKEN)
The token exists only after authentik applied the `ldap-wifi` blueprint (outpost `ldap-wifi`), so this one is a second step:
1. Push everything else first (the outpost Pod stays in an error state without the Secret, which is expected).
2. authentik admin UI: Applications > Outposts > `ldap-wifi` > *View Deployment Info* > copy `AUTHENTIK_TOKEN`.
3. Put it in `authentik-ldap-wifi-outpost.secret.yaml` as `token`, `sops -e -i`, add it to `kustomization.yaml`, push.
The token stays valid until the outpost is deleted; re-issue (and replace the Secret) if it leaks.

## Order summary
1. `wifi-db`, `step-ca-wifi`, `authentik` secrets, `freeradius-db`, `freeradius-ldap-wifi`
2. wait for authentik to apply the blueprint, copy the outpost token
3. `authentik-ldap-wifi-outpost`
