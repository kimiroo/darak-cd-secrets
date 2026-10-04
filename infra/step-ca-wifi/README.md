# step-ca-wifi

| Secret | Keys | Must match (in darak-cd, not in this repo) |
|---|---|---|
| `intermediate-wifi` | `password`, `ca_key` | `ca/intermediate_ca.crt` and `ca/ca.json` of the same PKI run |

All of them come from one run of `~/ca-wifi/regen-all.sh`: it writes `intermediate-wifi.secret.yaml` (plaintext, copy it here and `sops -e -i`)
and updates the public files in darak-cd (`root_ca.crt`, `intermediate_ca.crt`, `ca.json`, FreeRADIUS `wifi-ca.crt`, `ldap-ca.crt`).
**Never mix files of two runs**: a key from one run with certificates from another makes step-ca fail to start.

`password` is the intermediate key password, no newline at the end, in quotes. The root key and `admin.pass` are not stored in the cluster
(`admin.pass` is only used by the `eap` CLI). Move `root_ca.key` offline.

Order: encrypt, then add `- step-ca-wifi` to `../kustomization.yaml` (the directory `kustomization.yaml` already lists the file).
