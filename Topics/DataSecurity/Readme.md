#### Please have a read about [Vault Architecture](https://github.com/ArunNadda/HashiCorp-Certified-Vault-Associate-notes-learning-material/tree/master/Topics/Vault%20Architecture) before learning about how data is secured in Vault.


# Security of Data in vault

## Data protection and initialization

- **How does Vault protect data**
  1. Vault creates Encryption key
  2. encrypts data and stores the key along with data.
  3. Encrypts "encryption key" with "Master key"
  4. MasterKey, encryps/decrypts encryption key.
  5. MasterKey is stored in memory of Vault node, never stored in persistent storage.


- **Vault initialization**
  - Only need to initialize when vault is first installed.
  - prepares backend storage to receive data.
  - Only need to init a Vault cluster via a single node.
  - it is when Vault creates the Master MasterKey
    - Options to define threshold, key shares, recovery keys and encryption.
  - it is when root token is generated and provided. root token is initial authentication mechanism to Vault
    - should create new auth methods.
    - should never be used in production., should be revoked.


###### env: VAULT_ADDR should be set, for local vault setup (dev mode)
```
$ export VAULT_ADDR=http://127.0.0.1:8200
```
###### check status
```
$ vault status
```

###### Initialize Vault
```
$ vault operator init
```

## Vault Seal and Unseal
- Vault always starts in sealed state. i.e. it knows where to access the data and how to access it, but it cannot decrypt it.
- only status check and vault unseal are possible.
- Unsealing means vault construct the master key, so it can decrypt and read the data.
- this Master key is only stored in Memory.

- Sealing means - vault "throws away" the master key. - requires another unseal operation for vault to read data.
- can seal vault manually using CLI,API or UI.

- When to seal:
  - Key shards are exposed...
  - Network compromised...
  - SPyware/malware in Vault Node.


**Unsealing**
- Key Sharding
- Cloud Auto Unseal
- Transit Unseal


**Unsealing using Key Shards**
- masterkey is broken into multiple shards (using Shamir sharing algorithm).
- can provide number of shards and threshold while unsealing.
- Key shards can be given to multiple employees in org.
- require at least **threshold** number of keys to generate **MasterKey**.
- default option for unsealing - no config required.
- No single person should have all shard keys.
- can use PGP keys to encrypt individual shard keys.
- shards should not be shared online and should be kept encrypted (using PGP).

##### Commands
```
$ export VAULT_ADDR="http://127.0.0.1:8200"
$ vault status
$ vault operator init -key-shares=3 -key-threshold=2
$ vault operator unseal # can unseal from different systems/ssh
```

**Unsealing using Auto Unseal**
- Vault will use Cloud KMS solution/HSM to encrypt/decrypt the **MasterKey** and unseal Vault.
- config file identifies **particular key** to use for decryption.
- automatically unseals Vault upon service or node restart without additional intervention.
- open source and enterprise.

```
snippet of config file:

seal "awskms"{
  region = "REGION"
  kms_key_id = "KMSKEY"
}
```

**Unsealing with Transit AutoUnseal**
- Masterkey is encrypted using transit (uses transit secret engine) cluster (Vault

- may be in namespace or in root.
- support key roation
- available in both opensource and Ent.
- core Vault cluster must be highly-available.


```
snippet of config on all individual vault clusters

seal "transit"{
  address = "https://vault:8200"
  token = "S.xxxxxxx"
  disable_renewal = "false"

  //key configuration
  key_name = "transit_key_name"
  mount_path = "transit/"
  namespace = "ns1/"

  //TLS configuration
  tls_ca_cert = "/etc/vault/ca_cert.pem"
  tls_client_ert = "/etc/vault/client_cert.pem"
  tls_client_key = "/etc/vault/ca_cert.pem"
  tls_server_name = "vault"
  tls_skip_verify = "false"
}

```




## pros and cons
- key sharding requires manual intervention
- manual handling of shard keys.

- Cloud Auto Unseal, may cause vendor lockin
- in most cases its region locked.

- Transit Unseal, needs one more vault cluster to maintain.
- should be HA.

