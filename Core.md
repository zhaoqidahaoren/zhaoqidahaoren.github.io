## Introduction to TDOS core

### TDOS product matrix introduction
![python-success](../img/matrix.png)

## Product architecture

![python-success](../img/framework.png)

As shown in the figure, the product has 5 layers, from bottom to top, the storage layer, data layer, protocol layer, consensus layer, contract layer, and network layer.

The storage layer is mainly used to store account data and blockchain metadata, and the storage technology mainly uses file systems, LevelDB and relational databases.

The data layer is mainly used to process all kinds of data in the transaction, such as packaging data into blocks, maintaining the blocks into a chain structure, encrypting and hashing the contents of the blocks, digital signatures of the block contents and increasing time Stamp, construct the transaction data into a Merkle tree, and calculate the hash value of the root node of the Merkle tree.

The blockchain network is also based on the P2P network. Each node in the network has both a client role and a server role.

The contract layer is divided into two parts, one part is LotusVM (ie virtual machine), and the smart contract code deployed by the user runs in LotusVM. Smart contract is a general term for code running on Ethereum. A smart contract often contains two parts: data and code. The smart contract system encodes the agreement or contract and triggers execution by specific events. Therefore, in principle, it is suitable for security, trust, and long-term agreements or contract scenarios. In the Ethereum system, the default programming language for smart contracts is TypeScript, and it is easy for readers who have learned JavaScript to learn TypeScript. The other part is the built-in contract of the system. Users can cover the built-in contract of the system by introducing a customized jar package, which can be loaded without deploying and compiling, and even a custom consensus mechanism can be realized by rewriting the built-in contract of the system.

## SDK use

### java-sdk

See details:
https://github.com/TrustedDataFramework/tds-sdk.git

### js-sdk

See details:
https://github.com/TrustedDataFramework/js-sdk.git

## Deployment start

### Parameter Description
``` 
sunflower:
    assert: 'false' # Whether to enable the assertion, the default is false
    validate: 'false' # Whether to verify local data at startup, if set to true, the ledger data will be verified at startup
    libs: 'local/jar' # jar package path, used to load external consensus plug-ins
    secret-store: "" # The path of the certificate, if filled, a public key will be printed out after the node starts and enter the block. After the user generates a certificate file through the tool and saves it to this path, the node will successfully load the certificate
```
### Start steps
1. Install jdk, the version is required to be above 8
   http://openjdk.java.net/install/
2. Download the packaged jar file with the name
   sunflower-core-0.0.1-SNAPSHOT.jar
3. Enter on the command line

```
java -jar sunflower-core-0.0.1-SNAPSHOT.jar
```

### Start instructions

TDOS uses a yml file as a parameter configuration file, and environment variables can be used at startup, while SPRING_CONFIG_LOCATION specifies a customized configuration file. E.g:


```
export SUNFLOWER_CONFIG_LOCATION=$HOME/Documents/local0.yml,$HOME/Documents/local1.yml
java -jar sunflower*.jar
```

The file paths are separated by commas, and the following configuration will overwrite the previous configuration. In addition to environment variable configuration, you can also specify configuration files with command line parameters, such as

```
java -jar sunflower*.jar --spring.config.location=$HOME/Documents/local0.yml
```

The parameters in the configuration file can be overwritten with corresponding command line parameters. For example, the configuration spring.datasource.url in the configuration file can be overwritten with command line parameters at startup.

```
java -jar sunflower*.jar --spring.datasource.url="jdbc:h2:mem:test"
```

### Encoding format
Recursive Prefix Encoding (RLP): https://github.com/ethereum/wiki/wiki/RLP A binary serialization specification, the advantage is compactness, but the disadvantage is that it only supports encoding and decoding of content below 4G.

Java can use annotations to encode and decode rlp: https://github.com/TrustedDataFramework/java-rlp

Please refer to https://github.com/ethereumjs/rlp for using rlp encoding in javascript or nodejs


## Block structure

### Block header


| Field name    |   Class   | Description |
| ---- | ---- | ---- |
| version | int | Block version number PoA=1634693120,PoW=7368567,PoS=7368563 |
| hashPrev    |   bytes   | The hash value of the parent block |
|    transactionsRoot  |   bytes   | Merkelgen of transaction |
| stateRoot | bytes | State tree root |
| height | long | Block height |
| createdAt | long | The construction time of the block, expressed in unix epoch seconds |
| payload | bytes | Load has different meanings according to different consensus|
| hash | bytes | The hash value of the block |

### Block body
The block body contains multiple transactions, which are divided into two categories:

1. The coinbase transaction is a transaction automatically generated by the miner node. The transaction contains the public key hash of the block producer and the block reward. In a block body, it is always ranked first, and there is only one of this type;

2. Other transactions, this is the transaction broadcast in the public chain. The user constructs the corresponding transaction and broadcasts it in the p2p network. The miner node obtains the transaction in the transaction memory pool and packs it into the block when the block is generated.
3. If there are no other transactions when the miner node is packaging, there is only coinbase transaction in the block body, and the block is referred to as an empty block. If you do not want to produce an empty block, you can refer to the consensus parameters.

### Merkle Tree
The Merkle tree usually contains the underlying (transaction) database of the block body, the root hash value of the block header (the Merkle root), and all branches along the underlying block data to the root hash. The Merkle tree operation process is generally to group and hash the data of the block, and insert the generated hash value into the Merkle tree, and so recurse until only the last root hash value is left and recorded as the block header Merkle roots.

In the block body is the Merkel root of the transaction.

### The specific meaning of payload

1. For the PoA consensus, the node will sign the entire block after the construction of the block is completed, and then put the signature in the payload field of the block header

2. The payload of the PoW consensus block header is equivalent to the nonce of the Bitcoin block header, which is used for proof of work

3. The payload field of the PoS block header is empty

## Transaction

### Transaction structure


| Field name    |   Class   | Description |
| ---- | ---- | ---- |
| version | int | Transaction version number PoA=1634693120,PoW=7368567,PoS=7368563 |
| type    |   int   | 0=COINBASE,1=transfer, 2=contract deployment, 3=contract call|
|  createdAt  |   long   | The construction time of the transaction, expressed in unix epoch seconds |
| nonce | long | The sequence number of the transaction, the nonce of the coinbase transaction is equal to the block height|
| from| bytes | The public key of the transaction sender |
| gasPrice | long | Transaction fee price |
| amount | long | Transfer, the transfer amount of the latter when the contract is called|
| payload | bytes | Load |
| to | bytes |  The recipient of the transfer or the contract being called|
| signature | bytes | signature|
| hash | bytes | Transaction hash |


### Different meanings of amount

1.  For coinbase transactions, amount is the amount of economic reward
2.  For transfer transactions, amount is the number of transfers
3.  For contract deployment transactions, the amount must be 0
4.  For transactions invoked by the contract, the amount of amount will be transferred to the account of the creator of the contract

### Different meanings of payload

1. The payload of coinbase transaction and transfer transaction is generally empty, especially the payload of PoA consensus is filled in with the public key of the block producer
2.  For contract deployment transactions, the payload includes the wasm bytecode of the smart contract, the contract's ABI and constructor parameters, etc.
3.  For the contract call transaction, the payload is the binary parameter to call the smart contract, which needs to be constructed through the sdk

## Account model

###  Account structure

Both ordinary account and contract account are represented by the following structure


| Field name    |   Class   | Description |
| ---- | ---- | ---- |
| address | bytes | Account address, twenty bytes |
| nonce |   long   | Ascending sequence number |
|  balance  |   long   | Account balance, this field is always 0 for contract accounts|
| createdBy | bytes | The creator of the contract, this field is empty for ordinary accounts |
| contractHash | bytes | The hash value of the contract code, this field is empty for ordinary accounts |
| storageRoot | bytes | Contract storage tree root, this field is empty for ordinary accounts |


### Ordinary account

The address of the ordinary account is calculated by the public key.
Ordinary accounts can be represented by elliptic curve key pairs, including public and private keys. The default elliptic curve is sm2.


The address generation of an ordinary account is to perform a hash value calculation on the public key and take the last 20 bytes of the public key. The pseudo code is as follows:

```nodejs
h = sm3(publicKey)
address = h[len(h) - 20:]
```

Nonce is used to prevent replay attacks. When constructing a transaction with an ordinary account, it is necessary to ensure that the nonce of the transaction is equal to the nonce of the current account plus one, which can be expressed as follows with pseudo code:

```nodejs
nonce = getNonce(publicKey)
transaction['type'] = 1 # Here is the transfer transaction, it can also be 2 or 3
transaction['from'] = publicKey
transaction['nonce'] = nonce + 1
```

balance is the balance of the account

### Contract account

The contract account is generated when the ordinary account deploys the contract, and the generation of the contract address is represented by pseudo code:

```nodejs
b = sm3(rlp.encode([publicKey, nonce]))
address = h[len(b) - 20:]
```

The contract address is to encode the public key of the contract deployer and the nonce of the deployment contract transaction into rlp, calculate the hash value, and then take the last 20 bytes.

The contract account does not have a corresponding private key and public key. The nonce value of the contract account is equal to the nonce of the deployment contract transaction, and this nonce will not change anymore.

Each contract account has its own independent storage space. This storage space is actually a Merkel-Patricia tree. The state of the contract account can be represented by the root of the Merkel-Patricia tree, the storageRoot field.

## Consensus mechanism

### Introduction
The consensus engine is the engine of the blockchain and maintains the normal operation of the blockchain world. Consensus algorithm is to achieve the algorithm involved in common understanding, it mainly solves the problem of multi-point unified opinions. Common consensus algorithms include PoW (Proof of Work), PoS (Proof of Equity), VRF (Verifiable Random Function), etc.

The consensus mechanism of the blockchain is actually the evolution of the internal production relations of the autonomous ecosystem, and its evolution method is similar to the process of human history.

In the primitive society, human beings were formed in the form of tribes, with no classes, no exploitation, no oppression, and the distribution of the fruits of labor is more rewarding, corresponding to Bitcoin's POW consensus mechanism-the proof of work mechanism.

The stronger the hunting ability, the higher the chance of survival and the more decision-making power in the tribe. The corresponding mechanism here is PoW, and the workload proof mechanism means that the more work, the greater the profit, and the more decision-making power.

With the increasing number of tribal people and the gradual improvement of the level of productivity, after the emergence of product surplus, the polarization between the rich and the poor appeared, and the original consensus mechanism was also destroyed and replaced by a class society, forming the original state.

At this time, the corresponding consensus mechanism is POS, the equity certification mechanism. The more assets you have, the higher the decision-making power. The interests and rights of the country are concentrated in the imperial aristocracy.

But POS will bring about the Matthew effect. The stronger the stronger, the weaker the weaker. A group of people began to awaken and produce new ideas, and they began to resist and revolutionize, corresponding to the current capitalist and socialist countries.

The corresponding consensus mechanism has also evolved into DPoS, entrusted proof of equity. Deputies are elected by the people through voting, and they exercise decision-making power on behalf of the people, just like the parliamentary system of Western countries and the system of the People's Congress in China.

PoW and PoS are currently the most commonly used consensus mechanisms in public chains, but PoW and PoS are insufficient in system processing capabilities. It is mainly reflected in two aspects: one is that the block speed is not fast enough, for example, Bitcoin generates a block in 10 minutes on average, and Ethereum generates a block in 15s on average; second, the transaction capacity of each block is limited, such as Bitcoin The block size of is about 1M, which can probably include 2000 transactions.

### Type of consensus

TDOS supports POA, POS and POW. (Will continue to be updated in the future)

#### POA
##### POA parameter    

```yml
sunflower:
  consensus:
    name: 'poa' # Choose poa as the consensus mechanism
    genesis: 'genesis/poa.jsonc' # The url of the genesis block, the search priority is Network> File System> classpath 
    block-interval: '1' # Block interval, the minimum is one second
    enable-mining: 'true' # Whether to open mining
    private-key: 'f00df601a78147ffe0b84de1dffbebed2a6ea965becd5d0bd7faf54f1f29c6b5' # The private key of the node is in plaintext, it is recommended to use environment variables to load
    allow-empty-block: 'false' # Whether to allow empty blocks
    max-body-size: '2048' # Maximum number of transactions in a block
```

##### PoA's genesis block file

```jsonc
{
  "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000", // The hash value of the parent block

  "timestamp": 1572511433, // Block timestamp

  // Account address allowed to produce blocks
  "miners": [{
      "addr": "9cbf30db111483e4b84e77ca0e39378fd7605e1b"
    },
    {
      "addr": "9cbf30db111483e4b84e77ca0e39378fd7605e1b"
    }

  ],

  // Pre-allocated account balance
  "alloc":{
        "9cbf30db111483e4b84e77ca0e39378fd7605e1b": 1000000,
        "bf0aba026e5a0e1a69094c8a0d19d905367d64cf": 1000000
  }
}
```
##### POA joining and existing

Proof of Authority Consensus (also known as PoA Consensus) stipulates that nodes can participate in the blockchain consensus only after being authorized, and only authorized nodes can join the p2p network

In the genesis block corresponding to the poa consensus, miners contains the public keys of all authorized nodes, and only nodes with the corresponding private keys can join the p2p network and participate in the consensus

There are two special built-in contract addresses in the PoA consensus, one is 0000000000000000000000000000000000000003 and 0000000000000000000000000000000000000004. These two built-in contracts hold p2p-permitted nodes and nodes authorized to participate in consensus respectively. If other nodes want to join the p2p network, they need to construct a request transaction and send it to the corresponding built-in contract.

Take joining a p2p network as an example, the process is as follows:


1. Node B tries to join the p2p network, so the following transaction is constructed and sent to node A that has been approved


```json
{
    "version": 1634693120,
    "type": 3,
    "createdAt": "2020-07-29T07:16:40Z",
    "nonce": 1,
    "from": "Node B's public key",
    "gasPrice": 0,
    "amount": 0,
    "payload": "00", 
    "to": "0000000000000000000000000000000000000003",
    "signature": "****",
    "hash": "**"
}
```
2.Node A receives the request from Node B by checking the status of the contract, and agrees to join Node B, constructing the following transaction and sending it to the chain

```json
{
    "version": 1634693120,
    "type": 3,
    "createdAt": "2020-07-29T07:16:40Z",
    "nonce": 1,
    "from": "Node A's public key",
    "gasPrice": 0,
    "amount": 0,
    "payload": "01 splicing the address of node A" , 
    "to": "0000000000000000000000000000000000000003",
    "signature": "****",
    "hash": "**"
}
```
3.After node A passed the request of node B, because originally only node A was in the authorized node, the condition that more than 2/3 agreed was met, and node B joined the p2p permission node. If node C wants to join the p2p network again, it must be agreed by both node A and node B, because at this time, if only node A agrees, more than 2/3 of the agreement is not satisfied.

#### POS

##### POS parameter (PoS)

```yml
sunflower:
  consensus:
    name: 'pos' # Choose pos as the consensus mechanism
    genesis: 'genesis/pos.jsonc' # The url of the genesis block, the search priority is Network> File System> classpath
    block-interval: '5' # Block interval, the minimum is 1 second
    enable-mining: 'true' # Whether to open mining
    allow-empty-block: 'true' # Is there an empty block
    max-body-size: '2048' # Maximum number of transactions in a block
    max-miners: '10' #Maximum number of miners
    miner-coin-base: '9cbf30db111483e4b84e77ca0e39378fd7605e1b' # Miner income address
```


##### PoS Genesis Block File

```jsonc
{
  "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000", // The hash value of the parent block

  "timestamp": 1572511433, // Block timestamp
  "miners": [{
      // Miner = address + number of votes
      "addr": "9cbf30db111483e4b84e77ca0e39378fd7605e1b",
      "vote": 100000000
    }
  ],

// Pre-allocated account balance
  "alloc":{
        "9cbf30db111483e4b84e77ca0e39378fd7605e1b": 1000000,
        "bf0aba026e5a0e1a69094c8a0d19d905367d64cf": 1000000
  }
}
```
##### POS joining and exiting
PoS stands for Proof of Stake. In the PoS consensus, nodes with larger stakes have block packaging rights. The PoS here uses a built-in contract to save the number of votes received by each account, and the address is 0000000000000000000000000000000000000005.

In this built-in contract, each account is sorted according to the number of votes. Assuming sunflower.consensus.max-miners=10, the top 10 accounts that vote will get the block packaging rights.

PoS-related transactions include voting transactions and cancel voting transactions

1.Voting transaction

Suppose node A casts 1000 votes to node B, then node A needs to construct the following transaction


```json
{
    "version": 7368563,
    "type": 3,
    "createdAt": "2020-07-29T07:16:40Z",
    "nonce": 1,
    "from": "Node A's public key",
    "gasPrice": 0,
    "amount": 1000,
    "payload": "00 spliced on the address of node B" , 
    "to": "0000000000000000000000000000000000000005",
    "signature": "****",
    "hash": "**"
}
```

2. Withdrawal of voting

   Assuming that node A wants to withdraw its vote to node B, the following transaction needs to be constructed


```json
{
    "version": 7368563,
    "type": 3,
    "createdAt": "2020-07-29T07:16:40Z",
    "nonce": 1,
    "from": "Node A's public key",
    "gasPrice": 0,
    "amount": 0,
    "payload": "01 hash value of the voting transaction" ,
    "to": "0000000000000000000000000000000000000005",
    "signature": "****",
    "hash": "**"
}
```
3.Node A can vote again only after withdrawing the vote for B

#### POW
##### POW Parameter
```yml
sunflower:
  consensus:
    name: 'pow' # Choose pow as the consensus mechanism
    genesis: 'genesis/pow.jsonc' # The url of the genesis block, the search priority is Network> File System> classpath
    block-interval: '30' # Block generation interval, the minimum is one second,
    enable-mining: 'true' # Whether to open mining
    blocks-per-era: '100' # How many blocks to adjust the difficulty value
    allow-empty-block: 'true' # is there an empty block
    max-body-size: '2048' # Maximum number of transactions in a block
    miner-coin-base: '9cbf30db111483e4b84e77ca0e39378fd7605e1b' # Miner income address
```
##### PoW genesis block file

```json
{
  "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000", // The hash value of the parent block


  "timestamp": 1572511433, // Block timestamp

  "nbits": "0000ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff", // Initial difficulty value

  // Pre-allocated account balance
  "alloc":{
        "9cbf30db111483e4b84e77ca0e39378fd7605e1b": 1000000,
        "bf0aba026e5a0e1a69094c8a0d19d905367d64cf": 1000000
  }
}
```
##### POW mechanism
PoW is proof of work. Miners who first solve the hash value problem can get the block packaging right. The proof of work is expressed in pseudocode as follows:

```
while True:
  r = randbytes(32) # Generate random payload
  block['payload'] = r
  digest = hash(block)
  if digest < nbits: 
    # Proof of work completed
    break
```
The PoW here uses a built-in contract to save the difficulty value. The address of the built-in contract is 0000000000000000000000000000000000000002. The difficulty value will be adjusted at any time, unlike Bitcoin, which saves the difficulty value in the block header.

PoW does not impose restrictions on p2p, nor does it limit the number of miners. As long as the miners who can solve the hash value problem first can get the block packaging right.

## P2P network

P2P networks are based on grpc or websocket, both of which are binary protocols and both support long connections.

### Parameter configuration
```
sunflower:
  p2p:
    name: 'websocket' # The protocol used for P2P message transmission, the default is websocket, gRPC is optional
    max-peers: '16' # The maximum number of neighbor nodes is limited, the default is 16
    
    #  The address of the node, the port can be left blank, the default monitoring port is 30569
    #  If the host name is filled with localhost or 127.0.0.1, the program will first try to obtain the public network ip of the node. If the public network ip plus the port number can be pinged, the program will use the public network ip+port as the external address.
    # address: 'node://localhost:7000'  If the public network ip fails to obtain or the public network ip+port cannot be pinged, the program will obtain the ip of the local machine in the local area network and use the ip of the local area network as its external address
     address:'node://localhost:7000'

    # Whether to enable node discovery, if node discovery is not enabled, neighbor nodes are fixed as seed nodes and trusted nodes
    # If the node is turned on for discovery, neighbor nodes will dynamically change, but the trusted node will not be actively disconnected.     
    enable-discovery: 'true' 
    
    # Seed node
    bootstraps:
      - 'node://192.168.1.117:9999'

    # The address of the trusted node
    trusted:
      - 'node://192.168.1.117:9999'


    # Whitelist configuration, you can fill in the public keys of other nodes. If at least one public key is filled in the whitelist, the blacklist will be invalid and only the nodes in the whitelist can be connected
    white-list:
    	- '02b507fe1afd0cc7a525488292beadbe9f143784de44f8bc1c991636509fd50936'


    # Blacklist configuration, you can fill in the public key of other nodes, if a node’s public key is in the blacklist, its messages will not be received
    blocked-list:
    	- '02b507fe1afd0cc7a525488292beadbe9f143784de44f8bc1c991636509fd50936'

    # Set the private key of the node p2p in plain text. It is recommended to load the private key with a certificate file
    private-key: 'f00df601a78147ffe0b84de1dffbebed2a6ea965becd5d0bd7faf54f1f29c6b5'

    # Whether to persist neighbor node information, the default is false
    # If set to true and database.type is not memory, the node information will be persisted to the file database.directory/peers.json
    persist: false

    # discover-rate: 15

    # The protocol will sub-packet transmission of packets exceeding this size. See the p2p chapter for details
    max-packet-size: 2097152

    # Clean up the received sub-packet data every 300 seconds, see p2p chapter for details
    cache-expired-after: 300
```

### Message structure

&#160;The serialization and deserialization of p2p messages are based on protobuf

| Field Name     |   Class   | description |
| ---- | ---- | ---- |
| <div style="width:70pt">code</div>  | <div style="width:30pt">int</div> | NOTHING=0,PING=1,PONG=2,LOOKUP=3,PEERS=4,ANOTHER=5,DISCONNECT=6,MULTI_PART=7 |
| created_at    |   int   | Timestamp when the message was sent |
|    remote_peer  |   string   | uri of the sender of the message |
| ttl | int | Whenever a message is forwarded once, ttl will be reduced by 1, preventing the message from being forwarded indefinitely |
| nonce| int | Increment the random number to prevent messages with the same hash value |
|signature| bytes |The signature of the message sender on the whole message to prevent the content of the message from being tampered with |
| data | bytes | Message body, the specific content depends on code|

Remarks:
              NOTHING is an empty message and will not be processed
              PING and PONG are mainly used for node discovery, keep-alive and key negotiation. LOOKUP and PEERS are used for node discovery.
              DISCONNECT indicates that the node is about to be disconnected, and MULTI_PART is used to send the message in packets.


When code=ANOTHER, the content in data is generally structured data encoded by rlp

### Key agreement

For encrypted communication between node A and node B, a common key that only A and B knows needs to be negotiated. For node A, this key can be calculated using the public key of node B and the private key of node A, and for node B, this key can be calculated using the public key of node A and the private key of node B, so that After a PING and PONG, the ANOTHER type messages between nodes A and B can be encrypted with this key, the default symmetric encryption algorithm is sm4

###  Node discovery

Each node has its own elliptic curve key pair. Taking sm2 as an example, the length of the public key of sm2 is 33 bytes and the length of the private key is 32 bytes. The unique identifier of a node in the P2P network is its own public key, and this public key information is encoded in the uri of the node in hexadecimal.

E.g
```
node://03a5acb1faa4dfe70f8e038e297de499cb258cc00afda2822e27291ed180013bd8@192.168.1.3:9999
```

When a node is found to be down, the node does not process LOOKUP and PEERS messages, and only maintains a connection with the seed node or trust node.

When the node discovery is turned on, if you want to connect to the seed node at startup, you can add this uri to the seed node list. If the uri does not contain the public key, such as node://192.168.1.3:9999, the node will be established Obtain the other party's public key when connecting.

Node discovery is based on the kademlia protocol. In this protocol, each node has its own unique identifier, and the distance between nodes can be calculated through this identifier. This distance has nothing to do with the physical address of the ip address, only related to each other's public keys. According to different distances, nodes will divide other nodes into different buckets. For example, the length of the public key of sm2 is 33 bytes and 264 bits, so there are 264 buckets. The kademlia protocol can ensure that the connectivity of the network can be maximized when the neighboring nodes of a node are evenly distributed among different buckets.

The node will score its neighbor nodes according to the activity level, and send a PING message and a Lookup message to other nodes every `sunflower.p2p.discover-rate` seconds, and other nodes will reply PONG messages after receiving the PING message .

When node B receives the Lookup message from node A, it will return a PEERS message. The PEERS message contains node B's neighbor node information. Node A receives the PEERS message from node B and will try to connect to the neighbor node in the PEERS message.

When node A receives any type of message from node B, it will add 32 points to node B. The half-life of this point is also `sunflower.p2p.discover-rate` seconds. When node A and node B are continuously connected, The respective scores are always greater than 0. When node B actively disconnects from A, the score of B will quickly decay to 0. When the score of B is 0, node A thinks that node B has been for a long time. In the offline state, remove node B from the neighbor node list.

### Message splitting

Both websocket and grpc limit the size of a single message. In order to send a larger single message, the large message needs to be sent in packets. When the message size exceeds `sunflower.p2p.max-packet-size`, the large message It will be split into multiple messages with code MULTI_PART and sent successively. After receiving the message of MULTI_PART, the receiver will temporarily store the message in the memory until all the sub-packaging messages have been received, and the receiver will then send the received sub-packets. The package messages are merged and then processed later.


### Block synchronization and transaction broadcast

The block synchronization protocol is based on five different message types, and RLP encoding is used for transmission.

During the block synchronization process, the node maintains a priority queue. This queue contains all blocks waiting to be verified and written. The block at the head of the queue has the lowest height and contains more transactions.

The code in the following data structure is not related to the code in the P2P message, and the whole will be filled into the data field in the P2P message through rlp encoding.



1. Status


| Field Name     |   Class   | description |
| ---- | ---- | ---- |
| code | int | Status = 4 |
| bestBlockHeight    |   long   | Maximum block height|
| bestBlockHash  |   bytes   | The hash value of the highest block |
| genesisBlockHash | bytes | The hash value of the genesis block |
| prunedHeight| long | 压缩目标区块的高度 The height of compressed target block  |
|prunedHash| bytes | 压缩目标区块的哈希 The hash value of the compressed target block|


2. Get Blocks


| Field Name     |   Class   | description |
| ---- | ---- | ---- |
| code | int | GET_BLOCKS = 5 |
| startHeight    |   long   | Requested starting block height |
| stopHeight  |   long   | The requested end block height |
| descend | bool | true for descending order, false for ascending order |
| limit| long |Maximum number of blocks per request |


When the node receives the Status message, it will first try to synchronize the orphan block. If the height of the other party’s highest block is greater than the height of the orphan block, the node sends a block request: startHeight=local orphan block height stopHeight=the other party’s highest block height, descend=true,limit depends on the local block transfer limit

Secondly, the node will compare its own local highest block height with the counterpart's highest block height, if the local highest block height is less than or equal to the counterpart's highest block height, or the hash value of the local highest block and the counterpart's highest block If the hash values are not equal, the node will send a block request to the other party: startHeight=local maximum block height stopHeight=the other party’s highest block height, descend=false, limit depends on the local block transfer limit

3. Blocks

| Field Name     |   Class   | description |
| ---- | ---- | ---- |
| code | int | BLOCKS = 6 |
| blocks    |  array | block |


After the node receives the block request, it needs to respond to the request. The content of the response is the block body in the rlp encoding format. The node that receives the block will temporarily store the block in the queue to be written


4. Proposal

| Field Name     |   Class   | description |
| ---- | ---- | ---- |
| code | int | PROPOSAL = 7 |
| block    |  BLOCK  | block |


After the block is successfully generated, the miner node will broadcast the produced block to neighbor nodes. The node that receives the block will temporarily store the block in the queue to be written, and will relay the message to other neighbor nodes.

5. Transaction

| Field Name     |   Class   | description |
| ---- | ---- | ---- |
| code | int | TRANSACTION = 8 |
| transaction    |  Transaction  | Transaction |

 When a user sends a transaction through rpc, the node will broadcast the transaction to other nodes through p2p, and other nodes will put the transaction into the transaction memory pool after receiving it, and relay the transaction at the same time.

### Message flow limit

In order to prevent too frequent requests, the Status and Get Blocks messages will be limited during synchronization, and the default limit is 16hz.


### Fast synchronization and block compression

When the new node synchronizes from scratch, you can specify the target block in the configuration file for fast synchronization, and the node will enter the fast synchronization state after startup. Nodes in the fast synchronization state will give priority to the nodes that can provide fast synchronization of blocks and account states. After all the account states are downloaded, the node verifies the root of the state tree. If the state tree verification is successful, the rapid synchronization is completed, and the node Blocks can be produced and synchronized normally.


When the local block height is high and there is a large amount of outdated data, block compression can be performed to delete outdated block headers, transactions and account states. Block compression will not affect synchronization and block generation, but the deleted historical account data can no longer be queried. The message types used for fast synchronization are:


1.  Get account

| Field Name     |   Class   | description |
| ---- | ---- | ---- |
| code | int | GET_ACCOUNTS = 11 |
| stateRoot    |  bytes | The root of the state tree of the target block |
| maxAccounts | int | Maximum number of accounts for a single transfer |


2. account

| Field Name     |   Class   | description |
| ---- | ---- | ---- |
| code | int | ACCOUNTS = 12 |
| total   |  long | The total number of accounts, this field is meaningful only when traversed is true |
| accounts | array| account |
| traversed | bool | Has the other party completed the transfer of all accounts |

## Cryptographic algorithm
The cryptographic algorithm adopts the Chinese national secret algorithm, which was released by the Chinese State Cryptography Administration on December 17, 2010, and is mainly implemented as sm2, sm3 and sm4

### Introduction
#### SM2 

SM2 elliptic curve public key cryptographic algorithm is a public key cryptographic algorithm independently designed by China, including SM2-1 elliptic curve digital signature algorithm, SM2-2 elliptic curve key exchange protocol, SM2-3 elliptic curve public key encryption algorithm, respectively Realize functions such as digital signature key negotiation and data encryption. The difference between the SM2 algorithm and the RSA algorithm is that the SM2 algorithm is based on the point group discrete logarithm problem on the elliptic curve. Compared with the RSA algorithm, the strength of the 256-bit SM2 cipher is higher than that of the 2048-bit RSA cipher.

Elliptic curve parameters do not give recommended curves, and curve parameters need to be generated using certain algorithms. However, in actual use, Chinese State Cryptography Administration recommends the use of a 256-bit elliptic curve in the prime number domain. The curve equation is y^2= x^3+ax+b (where p is a large prime number greater than 3, and n is the order of the base point G , Gx and Gy are the x and y values of the base point G respectively, and a and b are the coefficients of the circular curve equation y^2= x^3+ax+b).

1.  Use the recommended curve SM2P256V1, the parameters of the elliptic curve are

```python
a = 'FFFFFFFEFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF00000000FFFFFFFFFFFFFFFC' # Coefficient a
b = '28E9FA9E9D9F5E344D5A9E4BCF6509A7F39789F515AB8F92DDBCBD414D940E93' # Coefficient b
p = 'FFFFFFFEFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF00000000FFFFFFFFFFFFFFFF' # Prime field Fp

Gx = '32C4AE2C1F1981195F9904466A39C9948FE30BBFF2660BE1715A4589334C74C7' # X coordinate of point G
Gy = 'BC3736A2F4F6779C59BDCEE36B692153D0A9877CC62A474002DF32E52139F0A0' # Y coordinate of point G
n = 'FFFFFFFEFFFFFFFFFFFFFFFFFFFFFFFF7203DF6B21C6052B53BBF40939D54123' # Order of point G
```

2. The public key is always saved or sent in compressed form, and the public key compression and decompression are based on "SM2 Elliptic Curve Public Key Cryptography Algorithm Part 1: General Provisions" Section 4 as the standard。

3. The SM2 signature uses ```userid@soie-chain.com``` as the userid, and the signature result is saved in a sequential splicing manner of r and s, with a total of 64 bytes.

4. The result of SM2 public key encryption is saved in the form of C1C2C3, where C1 is the uncompressed public key starting with 0x04, C2 is the encrypted ciphertext, and C3 is the SM3 digest

#### SM3

SM3 hash algorithm is a cryptographic hash algorithm independently designed by China. It is suitable for the generation and verification of digital signature and verification message authentication codes and the generation of random numbers in commercial cryptographic applications, and can meet the security requirements of a variety of cryptographic applications. In order to ensure the security of the hash algorithm, the length of the hash value generated should not be too short. For example, MD5 outputs a 128-bit hash value, and the output length is too short, which affects its security. The output length of the SHA-1 algorithm is 160 bits, SM3 algorithm The output length of is 256 bits, so the security of SM3 algorithm is higher than MD5 algorithm and SHA-1 algorithm.

#### SM4

SM4 block cipher algorithm is a block symmetric cipher algorithm independently designed by China, which is used to realize data encryption/decryption operation to ensure the confidentiality of data and information. The basic condition to ensure the security of a symmetric cryptographic algorithm is that it has sufficient key length. SM4 algorithm and AES algorithm have the same key length and the packet length is 128 bits, so it is more secure than 3DES algorithm.

SM4 complies with the standards in "SMS4 Password Algorithm Used by Wireless LAN Products". Encryption and decryption using ECB is always used, and the key length is 16 bytes. If you need to fill in the message, follow the steps below:

1. Calculate the length of the message in bytes, because the length of the message is less than ```2^32```, we will get a 32-bit unsigned integer ```l```

2. 
Use big-endian encoding to encode ```l``` into a 4-byte byte string ```arr0```

3. After calculating the length of the original message plus 4, the additive inverse modulo 16 ```m```

```typescript
const m = 16 - ((msg.length + 4) % 16) // The modular arithmetic symbol of C-style programming language is %
```

4. 
Construct a byte array ```arr1``` with a length of ```m``` and fill it with ```0x00```

5. Combine ```arr0```, ```msg``` and ```arr1``` to get the padded byte string ```padded```, and the length of the padded byte string must be certain Is a multiple of 16.

  

  In the same way, we can get msg from the ```padded``` byte string:

  1.Read the first four bytes, and get a 32-bit unsigned integer ```l``` according to big-endian encoding. This ```l``` is the length of the original message

  2.Continue to read ```l``` bytes to get the original message ```msg```

### Parameter configuration
```yml
sunflower:
  crypto:
    hash: 'sm3' #Hash calculation
    ec: 'sm2' #Asymmetric encryption
    ae: 'sm4' #Symmetric encryption
```
### Keystore
keystore Used to save the user's private key
```json
{
  "publicKey" : "02ef5b1a65b7f2afbc20ecd0f1400892ea8c4e2c86fd0491abcb8be8af7f1f6a41", // User's public key

  "crypto" : {
    "cipher" : "sm4-128-ctr", // Symmetric encryption algorithm used 
    "cipherText" : "f078aefe661bc4498d686216e263c77a9026dcb4249d935c0b26b0b289cab098", // Encrypted private key

    "iv" : "ce69a3f220a7d6e6eea04eb927e9f4d1", 
    "salt" : "cfa496f673a4eeb508d301822b35b867aa05533225774fd47509908e6357e0cd"
  },
  "id" : "39453f15-b72d-4599-a86e-6d7459fa19d7",
  "version" : "1",
  "mac" : "345a86d380a043d20f36712ebb4bed37bdab412e73a290cab5aee6203c2ef83a",
  "kdf" : "sm2-kdf",
  "address" : "c86d486ac528ff14c17b9a9190b4d3c79a0291a8"
}
```
The keystore generation process is represented by pseudo code:

```py
keystore = {}
sk = b'********' # Private key
password = '********' # Password entered by the user
salt = randbytes(32) # Generate random salt
iv = randbytes(16) # Generate random vector

key = sm3(salt + password.encode('ascii'))[:16] # Derive the key
keykeystore['salt'] = salt.hex()
keystore['iv'] = iv.hex()

keystore['ciphertext'] = sm4.encrypt_ctr_nopadding(key, iv, sk) # Encrypt and save the private key

keystore['mac'] = sm3(key + ciphertext).hex() # Generate mac to verify the correctness of the password

keystore['id'] == uuid()
keystore['version'] = '1'
```


​         The keystore reading process is represented by pseudo code:

```py
password = '********' # Password entered by the user
salt = bytes.fromhex(keystore['salt']) # salt
iv = bytes.fromhex(keystore['iv']) # iv
key = sm3(salt + password.encode('ascii'))[:16]

# Verify the entered password
mac = sm3(key + ciphertext).hex()
if mac != keystore['mac']:
  raise BaseException('Keystore parsing failed: wrong password')

cipher = bytes.fromhex(keystore['ciphertext'])
sk = sm4.decrypt_ctr_nopadding(key, iv, cipher) # Read the private key
```
<b>Note:For specific state secret algorithm and keystore calls, please refer to SDK usage</b>

## Virtual machine

### Instruction Set
The code in the Wasm binary file is also composed of instructions one by one. Similarly, the Wasm instruction also contains two parts of information: Opcode and Operands.


Wasm opcode is fixed to one byte, so it can represent up to 256 instructions, which is the same as Java bytecode.

The Wasm1.0 specification defines a total of 172 instructions, which can be divided into 5 categories according to their functions, namely:

Control Instructions，13.

Parametric Instructions，2.

Variable Instructions，5.

Memory Instructions，25.

Numeric Instructions，127.

### Executive structure
WebAssembly files (called modules) are generally composed of three pieces: magic number (fixed value: 0x6d736100, little-endian storage), version number (current value: 0x00000001, little-endian storage), and several sections.

Among them, each area is composed of three parts: type, length and actual data.

The WebAssembly binary format uses LEB128 encoding to represent integer and string length information. Because WebAssembly is transmitted on the network, in order to minimize the file size, the format uses the data compression format leb128 internally, which is a variable-length integer compression Encoding form.

Because in many cases, for example, the value stored in a 32-bit integer most of the time only needs an 8-bit byte to store, but it may require 32 bits in the future, so leb128 encoding can be used.

The method used by leb128 is to start from the low byte. The highest bit in each byte is not used to store the actual value, but used as a flag bit. When the bit is set, it means that the next byte has data, otherwise it means The next byte has no data, and the byte without data is directly omitted, thus achieving the purpose of data compression.
   	
### Temporary storage
Linear memory is a continuous storage area addressed by bytes, ranging from 0 to a variable memory capacity. This capacity is usually a multiple of the WebAssembly page capacity, and the WebAssembly page capacity is fixed at 64KiB (as far as possible in the future, an optional larger page capacity support may be added). You can use the grow_memory operator to dynamically increase the linear memory capacity.

Linear memory can be considered as an untyped byte array. How the embedder maps this array into the process's own virtual memory has not given a clear explanation. Linear memory is sandboxed. It does not rely on other linear memory, the internal data structure of the execution engine, the execution stack, local variables, and the memory of other processes.

Local variable

Each function has some fixed, pre-declared local variables, which occupy a single index space inside the function. 

Parameters are treated as local variables. Local variables have no addresses and linear memory aliases. Local variables have value types. At the beginning of the function, the type of the local variables is initialized to the corresponding zero value (the integer value is initially 0, and the floating point number is initially +0), and the formal parameters are initialized to the actual parameter values passed to the function.

- get_local: Get the current value of local variables

- set_local: Set the current value of a local variable

- tee_local: Similar to set_local, after setting the current value of the local variable, it returns the new value that is set

The details about the index space and types of local variables will be further clarified. For example, whether local variables of type i32 and i64 must be adjacent or separated by other variables, etc.

Global variable

Global variables store a single value of a fixed value type and can be declared as mutable or immutable. This provides WebAssembly with a memory location that does not intersect with any linear memory, so it cannot be aliased arbitrarily as a bit.

Global variables can only be obtained by integer indexing from the global index space defined in the module. Global variables are either imported or defined in the module. There is no difference between these two methods for subsequent global variable access.

- get_global: Get the current value of global variables

- set_global: Set the current value of a global variable

- set_global
 
 
 
 Setting the immutable global variable index will cause validation exception

### Compiler front end
Convert the front-end code into a .wasm file, then put the file into the payload of the deployment contract transaction and broadcast it to the node.

### Support language
typescript, rust, c, c++, javascript, golang

### Smart contract example

 <b> Note: For detailed smart contract deployment and invocation instructions, please refer to "[TDS Smart Contract Writing Guidelines (AssemblyScript)](./zh-cn/Contracts.md)". </b>


#### getting Started

  The following is an example of a smart contract hello world:

```typescript
import {DB, Result, log, RLP, Context} from "../lib";

const KEY = Uint8Array.wrap(String.UTF8.encode('key'));

// every contract should had a function named by init
// which will be called at most once when contract deployed
export function init(): void{
    log("contract deployed successfully by index.ts")
}

export function increment(): void {
    let i = DB.has(KEY) ?  RLP.decodeU64(DB.get(KEY)) : 0;
    i++;
    log("call contract successful counter = " + i.toString());
    DB.set(KEY, RLP.encodeU64(i));
}

export function get(): void{
    let i = DB.has(KEY) ?  RLP.decodeU64(DB.get(KEY)) : 0;
    Result.write(RLP.encodeU64(i))
}

export function getN(): void{
    let i = DB.has(KEY) ?  RLP.decodeU64(DB.get(KEY)) : 0;
    const args = Context.args();
    assert(args.method === 'getN', 'method is getN');
    const j = RLP.decodeU64(args.parameters);
    Result.write(RLP.encodeU64(i + j))
}

export function addN(): void {
    const args = Context.args();
    let i = DB.has(KEY) ?  RLP.decodeU64(DB.get(KEY)) : 0;
    i+= RLP.decodeU64(args);
    DB.set(KEY, RLP.encodeU64(i));
}
```
In this smart contract, the first method is the init method, which will only be called when the contract is deployed.

In the first line of the smart contract, the dependency of DB is introduced. In the smart contract, the storage of the contract can be operated through the DB. For example, the increament method will read an integer from the DB every time it is triggered, then add one to the integer, and then save To the DB.

After the contract is successfully deployed, if you want to call the increment method, you need to construct a transaction. The pseudo code for constructing a transaction is as follows:

```js
// To construct the payload, you need to spell the method length as the first byte before the ascii encoding of the method name
const method = Buffer.from('increment', 'ascii')
const prefix = Buffer.of([method.length])
const tx = {
    "version": 1634693120,
    "type": 3,
    "createdAt": "2020-07-29T07:16:40Z",
    "nonce": 1,
    "from": "your public key",
    "gasPrice": 0,
    "amount": 0,
    "payload": Buffer.concat([prefix, method]).toString('hex'), 
    "to": "contract address",
    "signature": "****",
    "hash": "**",
}
```
If you want to check the latest value of this i, you can call the following pseudo code

```js
const rlp = require('rlp') // https://www.npmjs.com/package/rlp
const BigInteger = require('bigi') // https://www.npmjs.com/package/bigi

// Constructing parameters also requires the method length as the first byte to spell before the ascii encoding of the method name
const contractAddress = '****'
const method = Buffer.from('get', 'ascii')
const prefix = Buffer.of([method.length])
const parameters = Buffer.concat([prefix, method]).toString('hex')
axios.get(`localhost:8888/rpc/contract/${contractAddress}?parameters=${parameters}`)
  .then(r => {
    const d = r.data.data
    // Because Result.write in the contract is the rlp encoding of i, it needs to be decoded again
    const i = BigIneger.fromBuffer(rlp.decode(Buffer.from(d, 'hex')))
    console.log(`i = ${i.intValue()}`)
  })
```
#### Method call
There are two ways to interact with the contract outside:

1.Initiate a request

If you want to add parameters while viewing the contract, you can pass it in binary format. For example, you need to call the getN method of the above contract and pass j as a parameter:

```js
const contractAddress = '****' // Contract address
const method = Buffer.from('getN', 'ascii')
const prefix = Buffer.of([method.length])
const j = rlp.encode(123456) // Specialize Uint8 Array with rlp
const parameters = Buffer.concat([prefix, method, j]).toString('hex')
axios.get(`localhost:8888/rpc/contract/${contractAddress}?parameters=${parameters}`)
  .then(r => {
    const d = r.data.data
    // Because Result.write in the contract is the rlp encoding of i, it needs to be decoded again
    const i = BigIneger.fromBuffer(rlp.decode(Buffer.from(d, 'hex')))
    console.log(`i = ${i.intValue()}`)
  })
```
By initiating a request to intelligently view the status of the contract, the storage of the contract cannot be changed. If you try to rewrite the DB while viewing the contract status, the interface will report an error

2.Construct Transaction

The storage state of the contract can only be changed by constructing a transaction, such as the method addN(). The transaction that calls this method is as follows:


```js
const rlp = require('rlp') // https://www.npmjs.com/package/rlp
const method = Buffer.from('addN', 'ascii')
const prefix = Buffer.of([method.length])
const args = rlp.encode(123456)

const tx = {
    "version": 1634693120,
    "type": 3,
    "createdAt": "2020-07-29T07:16:40Z",
    "nonce": 1,
    "from": "Your public key",
    "gasPrice": 0,
    "amount": 0,
    "payload": Buffer.concat([prefix, method, args]).toString('hex'), 
    "to": "Contract address",
    "signature": "****",
    "hash": "**",
}
```
If you call the contract by constructing a transaction, you can add parameters after the payload, and other parameters related to the blockchain can also be obtained through the Context class


```ts
const header = context.header(); // Get block header
const tx = context.transaction(); // Get current transaction
const contract = context.contract(); // Get the contract itself
```