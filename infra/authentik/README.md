# authentik (Wi-Fi LDAP related secrets)

| Secret | Keys | Must match |
|---|---|---|
| `authentik-ldap-wifi-cert` | `tls.crt`, `tls.key` (type `kubernetes.io/tls`) | `wifi` config `ldap-ca.crt` (CN/SAN `authentik-ldap-wifi.freeradius.svc`) in darak-cd (= this `tls.crt`, same `regen-all.sh` run) |
| `authentik-ldap-wifi-search` | `token` | **`freeradius-ldap-wifi` (`password`) in `infra/freeradius`**, same value |

- `authentik-ldap-wifi-cert`: mounted in the worker at `/certs/darak-ldap-wifi`; authentik imports it hourly (at :24) as the certificate `darak-ldap-wifi`.
  The blueprint `ldap-wifi` looks it up by that name. If the provider has no certificate after the first apply, run the certificate discovery
  (or wait for :24) and apply the blueprint once more.
- `authentik-ldap-wifi-search`: becomes the bind password of the service account `ldap-wifi-search` (blueprint token `ldap-wifi-search-bind`, read from the
  worker env `LDAP_WIFI_SEARCH_TOKEN`). Value: `openssl rand -hex 32`. The worker reads it when it starts, so restart `authentik-worker`
  after changing it.
- The service account, its token and the `search_full_directory` permission on the provider are created by the blueprint, nothing to do in the UI.
