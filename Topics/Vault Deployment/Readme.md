# Vault Deployment
## Dev Server Mode
- runs vault without config quickly
- runs in memory - no storage required
- Auto init and unsealed
- doesnt use TLS
- UI enabled
- listener - 127.0.0.1:8200
- unseal key is given
- K/V v2 secret engine mounted at secret/ path
- Automatically log in as root
- root token provided.


**should never be used in production env**


#### Deploy vault in Dev Server Mode
- download binary
- unzip, set path
```
$ vault server -dev

## open another session
$ export VAULT_ADDR=127.0.0.1:8200
$ vault status

$ vault kv put secret/akn name=akn
$ vault kv get secret/akn
```



#### Deploy vault in Production
- deploy one or more persistent nodes via config file.
- Multiple Vault nodes should be configured
- Deploy close to apps
- automate deployment


**ConfigFile**
```
Storage "consul" {
  address = "127.0.0.1:8500"
  path = "vault/"
}
listener "tcp" {
  address = "0.0.0.0:8200"
  cluster_address = "0.0.0.0:8201"
  tls_disable = "true"
}
seal "awskms" {
  region = "us-east-1"
  kms_key_id = "xxxxx-xxx-xxx-xxxx-xxxx"
}
api_addr = "https://10.0.0.4:8200"
ui=true
```
