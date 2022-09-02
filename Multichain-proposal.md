# Multichain Proposal for Cardano

# Introduction to Multichain

Multichain is the ultimate Router for web3. It is an infrastructure developed for arbitrary cross-chain interactions.

Multichain was born as Anyswap on the 20th July 2020 to service the clear needs of different and diverse blockchains to communicate with each other. Each blockchain has its own unique services that it provides, its own community and its own development ecosystem. For our industry to reach the next level for consumers, we need a fast, secure, inexpensive and reliable way to exchange value, data and exercise control between the chains.

The solutions developed by Multichain allow almost all blockchains to inter-operate. There is no restriction to Ethereum like chains (e.g. Binance Smart Chain), or different Layer 2 chains requiring finality to Ethereum (e.g. Polygon), or a network of Parachains (e.g. Moonbeam in the PolkaDot system), or Bitcoin types of chain (e.g. Litecoin), or COSMOS chains (e.g. Terra). These are either now all integrated, or on course for integration. With support for all ECDSA and EdDSA encrypted chains, Multichain is almost universally applicable as an interoperable layer.

Multichain is now the leader in the cross-chain field, with a rapidly expanding family of chains (currently 61) and daily volumes well in excess of $70 million ([https://](https://anyswap.net/)scan.multichain.org). Its sustained daily volume of more than $100 million, its Total Value Locked in excess of $5 billion and its thousands of daily users are testament to its popularity and security.

We take pride that we are an open source protocol([https://github.com/anyswap](https://github.com/anyswap)). Indeed to be properly described as decentralized as we are, it is essential to be open source, since otherwise how would anyone know that this was the case? We also have a strong belief in the principle of trustlessness. Anyone can use our crosschain services. They are controlled by code and there are no Externally Owned Addresses acting as a weak link.

# SMPC Network

The Multichain network comprises what are called SMPC nodes. They exist separately from any blockchain and collectively sign transactions, but a group of them must do so together and they each only ever know part of the key to make this happen. The SMPC nodes are run by different organisations, institutions and individuals and they are incentivized to perform their functions properly.

Multi-party threshold ECDSA (elliptic curve digital signature algorithm) based on [GG20: One Round Threshold ECDSA with Identifiable Abort](https://eprint.iacr.org/2020/540.pdf) and EdDSA (Edwards curve digital signature algorithm),including the implementation of approval list connected with upper layer business logic and channel broadcasting based on P2P protocol.

It includes three main functions:

(1) Key generation is used to create secret sharing ("keygen") without trusted dealers.

(2) Use secret sharing,Paillier encryption and decryption to generate a signature ("signing").

(3) Preprocessing data before generating signature.(“pre-sign”).

When issuing the keygen/signing request command, there are two modes to choose from:

(1) Each participant node needs to approve the request command with its own account.It will first get the request command from the local approval list, and then approve or disagree.

(2) Each participant node does not need to approve the request command,which is agreed by default.

In distributed computing,message communication is required between each participant node.Firstly,the selected participants will form a group,and then P2P communication will be carried out within the group to exchange the intermediate calculation results of FastMPC algorithm.

The implementation is mainly developed in golang language,with a small amount of shell and C language.Leveldb database is used for local data storage,and third-party library codes such as Ethereum source code P2P and RPC modules and golang crypto are cited.

The implementation provides a series of RPC interfaces for external applications,such as bridge / router,to call in an RPC driven manner.The external application initiates a keygen/signaling request(RPC call),and then calls another RPC interface to obtain the approval list for approval.When the distributed calculation is completed,it will continue to call the RPC interface to obtain the calculation results.

# Cardano Bridge Integration Design

Cardano will be integrated with Multichain router V3, which was lauched on June 4th 2021. The Router can allow assets to be transferred from all the chains to Cardano.

The SMPC nodes will manage a single address, which will store the cardano native tokens bridged to ethereum and will mint the wrapped tokens. The only scripts needed are related to the minting policy for those wrapped tokens. This minting policy would simply check the signature of the SMPC wallet.

There will be no scripts involved on the Cardano side. The SMPC nodes will manage a single wallet, and sign the transactions. 
The SMPC wallet will store the cardano native tokens bridged to ethereum. SMPC network will mint the wrapped tokens. The only scripts needed are related to the minting policy for those wrapped tokens bridged from other chains. This minting policy would simply check the signature of the SMPC wallet.

Tokens on different chains can be bridged to Cardano, but let’s simplify it to the scenario between Ethereum and Cardano.

## Bringing Ethereum tokens into Cardano

### **Sending ETH native tokens to Cardano**

The bridge receives tokens from Ethereum chain by minting a *wrapped* version of them in Cardano blockchain. The minting is performed by the SMPC wallet using the single-issuer policy.

Flow description:

- User sends **k** *tokens E* using the ETH side of the bridge.
- SMPC nodes build and sign a Cardano transaction that **mints** k *wrapped tokens E* and sends them to the user wallet.

### Sending back wrapped ETH tokens from Cardano to Ethereum

The user sends their wrapped tokens to the SMPC wallet. The corresponding ETH tokens are transferred to the user wallet on the Ethereum side of the bridge. The wrapped tokens will be burnt when there is incoming transaction from Ethereum. 

Flow description:

- User sends **k** *wrapped E* to the SMPC wallet.
- SMPC nodes send **k** *tokens E* to the user wallet on the ETH side.
- k *wrapped tokens E will be burnt when there is incoming transaction.*

## Bringing Cardano tokens into Ethereum

### **Sending Cardano tokens to Ethereum**

The user sends their cardano native tokens to the SMPC wallet, and then the SMPC nodes send the corresponding wrapped ETH tokens on the Ethereum side. The cardano native tokens keep locked until another operation needs to receive the tokens back from Ethereum. 

Flow description:

- User sends **k** *tokens C* to the SMPC wallet.
- SMPC nodes send **k** *wrapped tokens C* to the user wallet on the ETH side.

### Sending back wrapped Cardano tokens from Ethereum to Cardano

The user sends their wrapped tokens using the ETH side of the bridge. In the Cardano side, the SMPC wallet must have locked at least the number of tokens transferred on the ETH side. This is an invariant that must hold: *every wrapped version of a cardano token in the ETH side must have the corresponding cardano token locked in the SMPC wallet.*
Then the corresponding amount of cardano tokens is sent to the user wallet. 

Flow description:

- User burn **k** *wrapped C* in the ETH side.
- SMPC nodes build and sign a cardano transaction that sends k *tokens C* to the user wallet.

# Other Specification

1, Decimal

Because the value of token in Cardano is limited to int 64, we need to consider the decimal convert in the 3 categories. 

a, If the total supply can be expressed by int 64 with the default decimal. The wrapped tokens on Cardano will keep the default decimal as native token.
b, If the total supply can not be expressed by int 64 with the default decimal, the decimal on Cardano side will be set as 6. The Multichain protocol will convert the decimal.
c, If the int 64 with 6 decimal still can’t express the total supply of the token, the decimal on Cardano side will be set as 0. The Multichain protocol will convert the decimal. This need to be agreed by the token project team. 

2, Rate

The Multichain protocol does not send two users in one transaction. All the transactions will be processed one by one. So there is only one transaction (from other chains to Cardano) in one block.

3, Minimum ADA for every UTXO

In Cardano, every UTXO must contain a minimal amount of ADA. After evaluation, extra fee equivalent to 2 ADA will be added to the base fee for every transaction from other chains to Cardano. 

To ensure there is enough ADA on SMPC address to send transaction to users, the ADA balance on SMPC address needed to be monitored by Gas Fee Monitor Mechanism, which is monitoring the gas fees on all the SMPC addresses sending asset or calling smart contracts on chains.
