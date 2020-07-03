# Vault Architecture and overview

Vault is a complex system that has many different modules working together. lets learn about basic terms used in Vault.


**Storage Backend** - A storage backend is responsible for durable storage of encrypted data.   
**Barrier** - The barrier is cryptographic seal around the Vault. It ensures that only encrypted data is written out, and that data is verified and decrypted on the way in  
**Secrets Engine** -  is responsible for managing secrets. There are many different types of Secrets engines available in Vault. Simple one is *kv*.   
**Audit Devices** - used to manage audit logs.  
**Auth Method** - used to authenticate users/applications connecting to Vault.  
    - once authenticated, Auth Method returns the list of applicable policies which should be applied  
    - Vault takes an authenticated user and returns a client token that can be used for future requests  
**Policies** - Authenticated users get authorisation to perform different tasks using policies. Policies decide permissions to users/application on path.  
**Path** - every object stored in Vault has a path. this makes is easier to apply policies at different levels inside path.  
**Client Token** - A client token (aka "Vault Token") is conceptually similar to a session cookie on a web site  
**Secret** - A secret is the term for anything returned by Vault which contains confidential or cryptographic material.  
**Server** - Vault depends on a long-running instance which operates as a server  
    - provides API which clients interact with and manages the interaction between all the secrets engines ACL enforcement, and secret lease revocation	  

  

## High Level Overview:

A very high level overview of vault (ref: https://www.vaultproject.io/docs/internals/architecture):

![Alt text](../../images/Vault_Arch_layers.png?raw=true "Vault_Arch_Layers")


In this picture, There is a clear separation of components that are inside or outside of the **security barrier**. 

- Only the storage backend and the HTTP API are outside, all other components are inside the barrier. (need to keep in mind for exam)

    - The storage backend is untrusted and is used to durably store encrypted data.
    - When the Vault server is started, it must be provided with a storage backend so that data is available across restarts. 
    - The HTTP API similarly must be started by the Vault server on start so that clients can interact with it.

  
    

![Alt text](../../images/vault-shamir-secret-sharing.png?raw=true "Shamir-Secret-Sharing")

  
    

- Once Started, the Vault is in a **sealed** state. To perform any kind of operations, it must be unsealed by providing the unseal keys. 
    - During initialization an encryption key is created, which is used to protect all the data in Vault. 
    - This encryption key is protected by a **master** key. By default, Vault uses a technique known as **Shamir's secret sharing algorithm** to split the master key into 5 shares, with a threshold of 3 required to reconstruct the master key.
    - The number of shares and the minimum threshold required can both be specified. 
    - Shamir's technique can be disabled, and the master key used directly for unsealing. 
    - Once Vault retrieves the encryption key, it can decrypt the data in the storage backend, and enters the unsealed state. Once unsealed, Vault loads all of the configured audit devices, auth methods, and secrets engines.
 
    - After the Vault is unsealed, requests can be processed from the HTTP API to the **Core**. The core is used to manage the flow of requests through the system, enforce ACLs, and ensure audit logging is done.



**Storage Backends**
- location where Vault can store persistent data  
- defined in configuration file of Vault  
- various parameters can be used in config file for storage Backends  
- Multiple storage backends can be used  
    - some provides HA  
    - some provide better data protection  
    - Supported from HashiCorp (when using Ent)  
           - File/s
           - consul
           - raft


**Secrets Engines**
- store secrets (kv,..)  
- generate secrets (aws, azure,..)  
- encrypt data (pki, transit,..)  
- can enable multiple secret engines as needed  
- Secret Engines are isolated at specific path  
    - paths make possible to use same engine twice e.g. kv secret engine can be used multiple times at different paths.  
    - can interact with path by specifying full path.  


**Auth Methods**
When a client first connects to Vault, it needs to authenticate.   
- perform authentication to Vault  
- assign identity and policies to user/app  
- multiple can be enabled  
- once authenticated, vault uses **token** for subsequent requests.  
- **default auth method is _token_ **  
- Vault provides configurable auth methods providing flexibility in the authentication mechanism used. 
- **Human friendly mechanisms** such as **username/password** or **GitHub** might be used for operators 
- **applications** may use **public/private keys** or **tokens** to authenticate. 
- An authentication request flows through core and into an auth method, which determines if the request is valid and returns a list of associated policies.
- multiple auth methods can be used at any given time.
 

**Policies**
Policies are named ACL rules. 
    - the **"root"** policy is built-in and *permits access to all resources*. 
    - can create any number of named policies with fine-grained control over paths. 
    - Vault operates exclusively in a whitelist mode, meaning that unless access is explicitly granted via a policy, the action is not allowed. *this makes management of permission easy*
    - Since a user may have multiple policies associated, an action is allowed if any policy permits it. 
    - Policies are stored and managed by an internal policy store. This internal store is manipulated through the system backend, which is always mounted at sys/.
    
    

**Lease management** is critical, as it allows expired client tokens or secrets to be revoked automatically. 
Additionally, Vault handles certain partial failure cases by using write ahead logging with a rollback manager. 
