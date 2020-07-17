# Vault Configuration
## About Storage backends
- location of storage of persisent Vault data
- configured in main Vault config file in "storage" stanza - and desired params.
- not all storage backends are equal, some support HA, other are easy to manage.
- HashiCorp supported - Consul, Raft, In-Memory and File.


- Many Storage Backends Supported.
- how to select Storage Backend.


**Examples**:
- DynamoDB Storage Backend config:

```
storage "dynamodb" {
  ha_enabled = "true"
  max_parallel = 128
  region = "us-east-1"
  table = "Vault-Storage-Backend"
  read_capacity = 10
  write_capacity = 15
}
```

- Consul Client: runs on vault node (8500)
- enable vault to talk to client running on the server
- client knows about Consul Server and its state.

```
storage "consul"{
  address = "127.0.0.1:8500"
  path = "vault/"
}
```


**Storage Backends**
- **Backups**
- need to backup the storage backend.

- **Restore**
- need to restart storage backend.

RetriveBackup -> Deploy New backend -> Restore -> Launch Vault -> Configure -> Unseal.



## About Vault Configuration file
- server configured using a file.
- HCL or JSON
- includes stanzas and params to define variety of config options.
- using -config flag

```
$ vault server -config <location>
```


**What is configured in this File** (general use)
- Storage Backend
- Listener(s) and Port
- TLS certificate
- Seal Type
- Cluster Name
- Log Level
- UI
- Cluster IP and Port


**What is not configured in config file** - enabled inside the vault, once its up and running.

- Secrets engines
- authentication Methods
- Audit Devices
- Policies
- Entities and Groups


**Available Stanzas**:
- Seal
- listener
- storage
- telemetry

**Parameters**:
- cluster_name
- log_level (Trace, Debug, Error, Warn, Info)
- ui
- api_addr - for client redirection
- cluster_addr  - for request fwding.



**Example Config File**

```
storage "consul" {
  address = "127.0.0.1:8500"
  path = "vault/"
}
listener "tcp" {
  address = "0.0.0.0:8200"
  cluster_address = "0.0.0.0:8201"
  tls_cert_file = "/etc/certs/vault.crt"
  tls_cert_key = "/etc/certs/vault.key"
}
seal "awskms" {
  region = "us-east-1"
  kms_key_id="xxxx-xxxxxxx-xxxx-xxxx-xxxxx"
}
api_addr = "https://10.0.0.20:8200"
ui = true
cluster_name = "akn_cluster"
log_level = "info"
```
