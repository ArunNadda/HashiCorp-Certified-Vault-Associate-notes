Before diving deep into Vault, we should get answer to some basic questions like

- what is Vault? 
- what problems can it solve?
- how it compares to existing software ?


## what is Vault?
### Here is definition of Vault by HashiCorp itself -

Vault is a tool for securely accessing secrets. A secret is anything that you want to tightly control access to, such as API keys, passwords, or certificates. 
Vault provides a unified interface to any secret, while providing tight access control and recording a detailed audit log.


### Well,  
Vault is a Swissarmy knife for securing secrets, data, PKIs ..... Vault is a tool to store and then access secrets. it is used for Authenticating and Authorizing users, machines or applications. all the interactions with vault can be audited. 
it is distributed, scalable and extensible.


### Vault features 

- key/value secrets can be stored in Vault. Vault encrypts these secrets prior to writing them to persistent storage
   - Helps in keeping "secrets" secret!
- Vault can generate secrets on-demand for some systems, such as AWS or SQL databases.
   - Secrets will be created and will exist only when these are required.
- Vault can encrypt and decrypt data without storing it. 
   - using Vault as Encryption as a Service
- All secrets in Vault have a lease associated with them. At the end of the lease, Vault will automatically revoke that secret. Clients can renew leases via renew APIs.
   - Remove the secrets when these are not required.
- Vault has built-in support for secret revocation. Vault can revoke not only single secrets, but a tree of secrets
   - Remove secrets in scenarios of breaches. all the compromised secrets can be revoked with one command.
   
   
## what problems can it solve? - Use Cases.
### Key usecases of Vault ( There are multitude number of UseCases of vault)

 