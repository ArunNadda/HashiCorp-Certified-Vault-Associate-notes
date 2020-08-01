# Vault Auditing
## Audit Devices
- keep a detailed log of all auth requests and responses to Vault
- formatted using JSON
- info is hashed with a salt using HMAC-SHA256 to ensure secrets and tokens aren't ever in plain text.
- logs should be protected.
```
vault audit enable file file_path=/var/log/vault_audit_log.log
```
**Type of Audit Devices**
- File
  - appends logs to a file
  - doesnt assist with log rotation
  - use fluentd or similar tools to send to collector
- Syslog
  - writes audit logs to a syslog
  - sends to a local agent only

- Socket:
  - writes to a tcp, udp or unix socket.
  - unreliable
  - shouldnt be used where strong gaurantees are required.

- should have more than one audit devices enabled.
- Vault requires atleast one audit device to write the log before completing the vault requests- if enaled.
- can run Vault without audit device (but not recommended)

```
$ vault audit list
$ vault audit enable file file_path=/var/log/audit_vault.log
$ cat /var/log/audit_vault.log
```
