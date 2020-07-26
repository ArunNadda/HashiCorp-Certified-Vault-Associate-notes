# Vault access - CLI, GUI and API
## Accessing VAULT
##### Intro to Vault Access
- Three interfaces to access/interact with Vault: UI, CLI and API
- All features are available in API.
- Most calls from CLI and UI invoke HTTP API.
- UI must be enabled in config
- Authentication required


**Who uses What**
- EndUser - API,CLI,UI
- Application - API
- Service/Orchestration - API


UI:
- runs on port 8200
- allows:
  - K/V store
  - Auth Methods
  - Secrets engines
  - Groups and Entities
  - Policies (ACLs)
  - Direct Access to CLI
  - Replication (Ent)
  - Licensing (Ent)


CLI:
- can be accessed from vault node or another node (remote)
- **vault login**
- VAULT_ADDR=https://127.0.0.1:8200
- use vault -autocomplete-install
- vault -h

```
vault kv list test/vault
vault kv  get test/vault/name
vault kv put test/applicaiton1 username=akn

vault kv delete test/application1
vault kv list test
vault kv metadata get test/application1
vault kv metadata delete test/application1
vault kv list test
```


```
vault secrets enable aws
vault write aws/config/root \
access_key=xxxxxx \
secreate_key=xxxxx \
region=us-east-1
```

```
vault policy list

cat file.hcl
path "test/application1"
  capabilities = {"read","update","delete","create"}
}


vault policy write testpolicy file.hcl

vault policy list

vault write auth/userpass/users/testuser \
password=testpass \
policies=testpolicy


vault login --method=userpass username=testuser
**ask for pass**
```


#### API:
- REST API provides access to all of Vault's capabilities
- Can use **curl** or other http clients
- need to authenticate before read/writing data
- Vault will retur a API-Token, which should be used for future requests.


```
vault token lookup

cat data.JSON
{
  "password": "testpass"
}


curl --request POST --data @data.JSON http://127.0.0.1:8200/v1/userpass/testuser | jq

### will output clienttoken

curl --header "X-Vault-Token: $clientToken" --request LIST http://127.0.0.1:8200/v1/test/vault | jq
curl --header "X-Vault-Token: $clientToken" --request GET http://127.0.0.1:8200/v1/test/vault/tstcourse | jq


cat newpwd.JSON
{
  "username": "newakn"
  "password": "newpassword"
}

curl -header "X-Vault-Token: $clientToken" --request POST --data @newpwd.JSON http://127.0.0.1:8200/v1/test/vault/tstcourse


curl --header "X-Vault-Token: $clientToken" --request GET http://127.0.0.1:8200/v1/test/vault/tstcourse | jq

```
