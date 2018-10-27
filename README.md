# ICO, in a sharded fashion

How to implement ICO on the QuarkChain platform? How QuarkChain can solve the scalability problem with certain trade-offs?

Private ICOs are almost independent of the platform, so we focus on public ICOs. In this case issuing "tokens", balances management, buying and selling is controlled by a smart contract.

Smart contract definition from the [Wikipedia](https://en.wikipedia.org/wiki/Smart_contract)

> A smart contract is a computer protocol intended to digitally facilitate, verify, or enforce the negotiation or performance of a contract. Smart contracts allow the performance of credible transactions without third parties. These transactions are trackable and irreversible.

Security is one of the most important aspects. Some vulnerabilities in code could only be fixed by hard forks. That is why many companies do not reinvent the wheel, but create their contracts based on the [OpenZeppelin library](https://github.com/OpenZeppelin/openzeppelin-solidity).

What is QuarkChain we can find out form the site [developers.quarkchain.io](https://developers.quarkchain.io/) 

> QuarkChain is an innovative permissionless blockchain architecture that aims to provide a secure, decentralized, and scalable blockchain solution to deliver 100,000+ on-chain TPS.

> QuarkChain fully supports Ethereum smart contract enabling developers to write and debug smart contract quickly using popular tools like remix and build scalable DApps that will be free from network congestion. 

If QuarkChain supports Ethereum smart contract then ICO can be easily implemented on this platform.

## ICO on QuarkChain vs. ICO on Ethereum

Shards in QuarkChain are independent of each other in case of smart contract execution. See [Wiki](https://github.com/QuarkChain/pyquarkchain/wiki/Smart-Contract)

> If a smart contract calls another smart contract in the shard, both caller and callee smart contracts must have the same full shard id. Otherwise, such call will fail. This guarantees that both smart contracts will always be in the same shard no matter how the network is resharded.

At the moment there are no special VM commands that would allow to communicate between different shard. For ICO this is not good news at all, because token contract has no chance to update balance on another shard. Balances become dependent on the shards and the smart contract cannot protect balances anymore. Three ways come to mind.

### 1. External manipulations

If we add ```increase_balance()``` and ```decrease_balance()``` methods then it would be possible to transfer tokens from one shard to another. The main drawback here are the security issues and centralization, because there will be no way to control the owner and keep him away from bad movements. Account may also be compromised.

### 2. Independent smart contracts

If the same smart contract is deployed on different shards then it will be several independent ICOs instead of one. Soft and hard caps become irrelevant in this case. Moreover all tokens will be different and have independent exchange rate. If new shard is added then it will be impossible to redistribute tokens from the existing shards.

### 3. ICO on a single shard

Instead of deploying smart contracts on every shard it is possible to use just one shard and conduct the ICO like before. But what to do with tokens next? Limit them to this one shard forever?

### Other issues

There are several other issues that must be taking into consideration.

Smart contract addresses generation procedure is different from the EVM, because shard ID is used. Potentially this could lead to problems with certain tools.

Even if smart contract addresses are generated the same way it is done in EVM, then it would be not easy to have the same address for the address for all shards because of the Nonce part. This will ruin the rule from the [Wiki](https://github.com/QuarkChain/pyquarkchain/wiki/Address%2C-Shard-Id%2C-Branch):

> With the same function you can build addresses of the same recipient for different shards. This will allow a single recipient to receive QKC in any shard.

This is correct for "user" accounts, but for smart contracts it is not correct. Moreover on different shards there could be different smart contracts or versions of them.

So in general there would be different addresses of the smart contract for each shard. This means that every smart contract that wants to use tokens must be customized for addresses of a particular shard. And when new shards are added such DApps must wait until tokens smart contracts are deployed. The latter problem could be solved with [Supporting Multi-Native-Tokens in a Single Blockchain](https://medium.com/quarkchain-official/supporting-multi-native-tokens-in-a-single-blockchain-686fc65f5931).

## What to do?

We could add syntactic sugar for ICO smart contracts generation, store all addresses of smart contracts across all shards and get their balances. But it will not solve a problem of ICO. Moreover it is not just an ICO problem. Any smart contract that need to have a synchronized data will face the same issue. It is not a simple decision: clone smart contract across shards and have many independent DApps or use only one copy on a single shard and believe that there will be no congestion on that shard.

An improvement of the QuarkChain architecture is required to provide an effective development and execution of multishards DApps.
