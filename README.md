# specs
Dinfra Specification

## System architecture
```
|-----------------------------------------------------------------|
| layer 4 |       Website | SDK | Tools | Docs | Watchmen         | 
|-----------------------------------------------------------------|
| layer 3 | DBs (scalable) | Apis (scalable) | Account system     |
|-----------------------------------------------------------------|
| layer 2 |           syncers( monitor ) / promisors              |
|-----------------------------------------------------------------|
| layer 1 |           chainreaders and full-nodes                 |
|         |    for Polkadot parachains, Polkadot realy chain,     |
|         |            Filecoin chain and IPFS network            |
|-----------------------------------------------------------------|
```

## Components
### chainreader
**Description**: Through the way of blockchain full node plugin or independent processes, the required blockchain data is pushed to `syncer` in real time. Each chain can have multiple chainreaders to provide data. This is the primal source of all data that will flow in all systems.

**High Availability considerations**: To ensure that chain data is always pushing data into your system, you can run multiple chainreaders, and syncers are designed to eliminate any identical information.


### syncer
**Description**: `syncer`'s main task is to store blockchain data into DBs. It gets data from all available `chainreaders` in order to get a complete view of all possible forks. In addition, other functions of `syncer` include:
- Node status monitoring: Monitor the status of multiple nodes of each blockchain
- Node selection: Select a set of nodes in good condition
- Fault alarm: Report failure node

**High Availability considerations**: Each `chainreader` can push data to multiple `syncer`s at the same time, and `syncer`s can be run in the master-slave mode.

### promisor
**Description**: The `promoter` is used to ensure that the transaction sent by the user is executed as soon as possible. The promoter will push the transaction continuously. Unless the transaction fails or expires, `prmisor` will push the transaction until it succeeds

`promisor` allows the user to set the expiration time.

**High Availability considerations**: Running multiple `promoter` nodes.

### DBs
**Description**: Different databases are needed for different components of Dinfra. The two most important databases are `reversible database` and `inreversible database`. `reversible database` is used to store active data of blockchains, which may be forked out. `inreversible database` is used to store irreversible historical data of blockchains, and is used for persistent storage. These two databases need to support high availability and automatic expansion

Alternative databases include: Google Cloud BigTable, TiKV.

**High Availability considerations**: Using databases with high availability and easy expansion.

### Apis
**Description**: Dinfra will provide a server process serving end-user requests in the GraphQL format. It speaks GraphQL over both HTTP and gRPC. It routes most of its request to the appropriate service. 

**High Availability considerations**: This process is stateless, and can be scaled up or down for the desired throughput. 

### Account system
**Description**: Each user must register and recharge before they can use the API service provided by Dinfra. We will accept the recharge of DOT token, and users can view the relevant information of users through the user management page. 

Users can purchase services according to the length of usage or the number of API calls.

### Watchmen
**Description**: Watchman is used to monitor whether the nodes and services of the whole system are normal or not, and alarm the abnormal state.

### SDK
**Description**: We need to provide SDKs in multiple languages to facilitate users to use Dinfra's API services. We will first provide the Js version of the SDK, followed by golang and Java.

