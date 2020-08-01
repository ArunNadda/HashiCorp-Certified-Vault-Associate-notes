# Vault HA/DR
## Vault Clustering and Replication
#### Types of Vault Nodes

- **Active Node**:
- **Standby Node**:
- **Performance Standby**: Ent license

#### Vault Clustering
- Vault provides build-in HA in the form of clustering.
- Multiple Vault instances form a cluster when sharing a storage backend without additional config.
- The storage backend must support HA.
- Clustering is available in both OS and Ent.
- Cluster consists of Active and Standby node
- How the leader determined.
- First nodes comes online is leader (the one which locks the storage)
- Vault is generally front-ended with a LB or Consul
- LB is for HA not LB. ( cos only single Acitve nodes handles all traffic)
- Health checks on LB can determine which node is the active node.
- curl https://<ip>:8200/v1/sys/health
  - HTTP response 200 = initialized, unsealed and active.
  - HTTP Respnse 429 = unsealed and standby
  - HTTP 503 - sealed.
- Vault can be tightly integrated into Consul as well
  - **Active Node:** active.vault.service.consul
  - **Standby Node:** standby.vault.service.consul



#### Disaster Recover Replication
- Replicates the following data;
  - K/V store
  - Policies
  - Tokens

- Warm Standby
- Does not serve client Requests
- DR Replication is always manual.
- primary cluster serves both read/write request.
- DR Cluster does not serve any of these


#### Performance Replication
- Replicates the following data:
  - K/V store
  - Policies
  - **Does NOT** replicate tokens

- Will Serve Client Requests (reads)
- Used to 'extend' vault across data centers or provide multi-cloud access




#### Replication and Filters
- Mount filters


#### Vault Ports and Protocols
- TLS encrypted
  - tcp/8200 (general port for UI and API)
  - TCP/8201 (used for server to server communications)


- Consul client requires additional ports
  - TCP/8500 - consul API
  - TCP/UDP 8301 - serf LAN Gossip



#### Vault Deployment Strategy
- Standard 3 x 6 deployment
