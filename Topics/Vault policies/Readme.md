# Vault policies
## Vault Policies
- similar to RBAC for certain Paths
- provide authorization, using HCL/JSON , are declarative
- Permissions
  - Create
  - Read
  - Update
  - Delete
  - List
  - Deny
  - Sudo

- Deny by default
- needs PATH and capabilities.
- can include variables/parameters
- cumulative and capabilities are additive
- Deny always take precedence
- follow principle of least priv.
- May need to add list perms for UI.


## attaching policy
## Default policies
- two policies create by default
  - Root
  - Default


## Custom policies
##

## Examples

1. Simple:

PATH: secret/apps, secret/common

path "secret/apps/application1/app-sercet"
{capabilities = ["read", "update"]
}


**Using '*' and '+'**

- The glob ( * ) is a wildcard and can only be used at the end of a path
- can be used to signify anything "after" a path or as part of a pattern
  - secret/apps/appliation1/*
  - secret/path/db-*

- the plus (+) matches any number of characters within a single path segment (secret/+/db)
- can use multiple times in path segment ( secret/+/+/db)



**Examples**

- using +
```
path "secret/apps/+/secret" {
  capabilities = ["read", "update"]
}
```

- all using *
```
path "secret/apps/*" {
  capabilities = ["read", "update"]
}
```

- Deny
```
path "secret/apps/*" {
  capabilities = ["read", "update"]
}

path "secret/apps/super-secret" {
  capabilities = ["deny"]
}
```

- variables

```
path "secret/apps/app1/*" {
  capabilities = ["read", "update"]
  allowed_parameters = {
    "secret" = []
  }
}
```

- variables (true or false)
```
path "secret/apps/app1" {
  capabilities = ["read", "update"]
  allowed_parameters = {
    "encrypted" = ["true", "false"]
  }
}
```


#### Administrative Policies
- policies applies to /sys path
- users/admins policies - define what they can do within Vault to administer Vault itself.
    - Unsealing
    - Changing policies
    - Adding secret backends
    - Configuring database configurations.

```
# Configure License
path "sys/license" {
  capabilities = ["read", "list", "create", "update", "delete"]
}
# Initialize Vault
path "sys/init" {
    capabilities = ["read", "create", "update"]
}
# Configure UI in Vault
path "sys/config/ui" {
    capabilities = ["read", "list", "sudo", "update", "delete"]
}
# Allow rekey
path "sys/rekey/*" {
    capabilities = ["read", "list", "update", "delete"]
}
# Allows key rotation
path "sys/rotate" {
    capabilities = ["update", "sudo"]
}
# allow seal
path "sys/seal" {
    capabilities = ["sudo"]
}
```


- Policies for UI
- UI, needs LIST permissions, without LIST users cannot browse
- LIST doesnt allow user to READ.

```
path "secret/apps/apps/secret" {
    capabilities = ["read", "list", "create", "update", "delete"]
}
path "secret/*" {
  capabilities = ["list"]
}
```

- use commands to modify policy
```
$ vault policy read test_policy
$ vault read auth/userpass/users/testuser
$ vault login $TOKEN


$ vault policy write test_policy_1 <filename>
$ vault policy list
$ vault policy test_policy_1

$ vault write auth/userpass/users/testuser policies=test_policy_1
$
```
