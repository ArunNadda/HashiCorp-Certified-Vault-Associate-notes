## Secrets Engines

**Static Secrets**
- never expires, required by legacy apps
- Secrets are often shared, no accountability
- secrets are valid 24x7, frequencly targeted by penetrators
- these secrets are rarely, if ever rotated. (mostly manual)
- Long-Lived, tend to live perpetually, result of technical debt, employee turnover...etc


**Dynamic Secrets**
- create on demand, valid/exists only when required
- associated leased
- validity period (time or number of times use)
- Technical Debt - solved
- Renewal
- Revocation


Example:
- CI/CD pipeline Access to public cloud
- privileged account for vulnerability scanning or administrators.
- Database credentials for applications.

### Intro to Secret engines:
- without secret engiens there is no point in deploying Vault.
- can store, generate or encrypt Data
- Many SE's can be enabled and used as needed.
- Secrets are enabled and isolated at a "path"

```
$ vault secrets enable aws
```

- All interactions are done directly with the "path" itself.

```
$ vault read aws/creds/aws_role
```




**Examples**
- Key/Value(KV) Secrets engine
  - stores any info like key and Value
  - e.g. - secrets/app1/creds
      - user:testuser
      - password:pwd123!
- mostly deployed.
- Two versions - V1 and V2
  - KV v1
  - KV v2 (supports versioning)
- KV Structure
  - best practices to use KV
  - as per requirements.
  - try to make generic for policy creation and admins.
  - can mount multiple KV SEs if required.
  - group by Apps/Teams?

```
$ vault secrets list
$ vault secrets enable -path=akn kv
#$ vault secrets enable -path=akn -version=1 kv
$ vault secrets enable -path=aknv2 -version=2 kv

$ vault kv akn/apps/akn_sec akn=pwd123#

$ vault kv list akn
$ vault kv list akn/apps
$ vault kv get akn/apps/akn_sec

$ vault kv put akn/apps/akn_sec akn=pwd321!
$ vault kv get akn/apps/akn_sec
$ vault kv put akn/apps/certs/db cert=@cert.pem ## refer file.


$ vault secrets list --detailed ## check if kv is v1 or v2

$ vault kv enable-versioning akn ## upgrade to v2
$ vault kv get akn/apps/akn_sec ## see more data, metadata
$ vault kv put akn/apps/akn_sec akn=pwd789$


$ vault kv get akn/apps/akn_sec
$ vault kv get -version=1 akn/apps/akn_sec
$ vault kv delete akn/apps/akn_sec #does softdelete
$ vault kv undelete -versions=2 akn/apps/akn_sec
$ vault kv metadata get akn/apps/akn_sec
$ vault kv metadata put -max-versions 2 akn/apps/akn_sec


$ vault kv metadata delete akn/apps/akn_sec
$ vault kv list akn/apps
$ vault kv get akn/apps/akn_sec
```


#### AWS Secrets Engine
- Dynamically generates AWS creds
- Still bound to a policy to permit/restrict actions
- configure for basic usage is simple
- when using for multiple accounts, it becomes trickier
- Three types of ways to generate creds:
    - iam_user - use with access and secret key
    - assumed_role - uses sts:AssumeRole to generate creds
    - federation_token - sts:GetFederationToken

**IAM User**
**Assume Role**
