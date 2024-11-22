Account Abstraction is going to revolutionize the biggest problem that hasn't been solved for over 5 years - Wallet UX. Learn everything you need to know about Account Abstraction on Ethereum, all the way up to building an ENTIRE wallet from scratch!

Self custody wallets
However, achieving self custody is a challenging task, even for tech savvy individuals. Managing private keys that are not human readable or long 24 word mnemonic phrases securely is hard, and if they ever get leaked at any point - say goodbye to any funds in those wallets.


Account Abstraction was born out of the experience of self custody wallets today being extremely annoying and easy to mess up with. The question being asked was - Can we do something better?

In Ethereum, there are two types of accounts:

**Externally Owned Accounts (EOAs)**: These accounts are controlled by private keys and managed by wallets. Private keys are typically generated through seed phrases. EOA accounts are the only ones which can initiate transactions and are required to pay gas fees in the native token for transaction processing.

Smart Contracts: These accounts are not linked to private keys, and are instead controlled by code. They cannot initiate transactions on their own, but can do everything else an EOA can.

Key mgmt is hard
Individuals often forget passwords - which are usually less complex than a private key or seed phrase - so memorizing keys and mnemonics by heart is not an option for most people. So what do they do?



Some people store it on the cloud, some store it on their email, some save it in a password manager, some write it on pieces of paper - but all of these come with their own tradeoffs and security risks. The problem is, one tiny mistake can cause these to get leaked - and if they get leaked, everything is gone. Recovery is impossible and this turns off a large number of people who have bad experiences in crypto because of this.

As a result, many people continue to opt for custody wallets at exchanges or other forms of custodial wallets where someone else owns the private keys of the account where you store your money. As we saw with FTX, this is highly dangerous as it's not truly your crypto in that case - you may as well share your private key with a random stranger on the internet and hope they don't run away with the money.

### Bad UX in custodial
In smart contracts we can define a function that does calls to other external smart contracts. A single function call to our smart contract can result in it calling 1, 2, 5, 10+ any number of external smart contracts through that single function call.


Unfortunately, externally owned accounts do not share that privilege. An EOA can only do one function call in one transaction when it initiates the transaction. That one function call may further do many more, but the EOA itself cannot construct a transaction that does more than one function call at a time

Due to this, we have ended up with some bad patterns. Things like the approve and transfer flow for ERC-20 - which causes users needing to do two transactions to make a simple swap on Uniswap. 

The repetitive process severely impacts user experience. In some cases, it is even more annoying - for example, on chain gaming. Imagine having to sign a transaction every time you make a move in a game. Wouldn't you feel frustrated and annoyed? I sure would.


Additionally, since users are also responsible for paying gas fees when using EOAs, they always must maintain some balance of the native token. This can get annoying when perhaps you want to bridge DAI, for example, from mainnet to a Layer 2 for cheaper transactions, but then also need to somehow acquire the native token to be able to do anything with the bridged DAI on there.



Externally owned accounts also lack access control, which means they have no multi-sig capabilities or anything like that on the account level. Anyone who has the private key has complete control of everything.

Over the years, various attempts have been made to address these issues through sideline solutions. For instance, EIP-2771 was introduced to enable gasless transactions. Smart contract wallets like Argent attempted to incorporate social recovery at the application level. However, none of those solutions proved to be completely effective, and MetaMask remains the most popular Ethereum wallet today.

And therefore, a need arose to build a comprehensive solution to address these problems once and for all and change the definition of what we consider an "account".

**Account Abstraction can be effectively considered as considering each "account" to be a smart contract, instead of an EOA. This smart contract can contain custom logic, which can be quite arbitrary, depending on how it is set up.**

The most significant idea of account abstraction is also the ability to separate the signer from the account. That is, the "thing" responsible for signing messages is no longer necessarily the same as the account itself.

This by itself brings with it a variety of possibilities, for example:

Having multiple signers for a single account

Having signature schemes that are not ECDSA for your account (i.e. using FaceID/TouchID to do transactions instead of using your Ethereum private key to sign messages)

Having different transaction validation methods for different types of transactions

Think of it this way - an account now has the ability to define it's own logic on what it considers a "valid" transaction because it is now a smart contract, and anyone can write their own smart contracts. It can also contain it's own logic to decide how to pay fees, how to initiate transactions, and much more. Frankly, it is up to us now to unleash our creativity and build even more exciting functionalities.

To access these smart contract accounts, we would utilize some sort of smart contract wallet. For example, Soul Wallet, Candide Wallet, and Argent are smart contract wallets that come with certain smart contract account implementations built in.

As we progress through these lessons however, in the last lesson we will build our own smart contract account in Solidity and then also create a simple wallet frontend for it which should give you a very in-depth understanding of everything. But I'm getting ahead of myself, let's go back to the topic at hand.

## Use caseas of AA
Gas Fees without Native Tokens

Separating the signer and the account allows us to customize how to handle gas payments for transactions. The signer can opt to pay the fee in an ERC-20 token, while the account with custom code can internally convert that ERC-20 token to ETH by conducting a swap on a DEX and use the received ETH to pay the network. This is possible because the account can incorporate custom code - including code to swap ERC-20 for ETH for gas - for transaction validation and execution.

Gas Sponsorships

Similar to the above example, we can explore gas sponsorship models where the signer only needs to sign the transaction without paying for gas themselves at all. Instead, the account's transactions can be sponsored by a third party.

For example, say Uniswap wants to cover the gas cost for swapping tokens for their top 100 traders every month. In this case, the signer simply needs to sign a transaction and the account can swap tokens on Uniswap without any gas attached because Uniswap will take care of the gas payment on their end.

Transaction Batching

Since your account is now a smart contract, you can also finally have the ability to execute multiple function calls in a single transaction. For example, you no longer need to do an approve and then a transfer to do a swap on Uniswap, or you no longer need to do an approve and then a transfer to list up an NFT for sale on OpenSea - you can do both actions in a single function call through your smart account instead.

With transaction batching like this, a lot of actions which would previously take multiple transactions to be initiated by the user can happen in a single transaction, which can improve UX significantly.

Access Control

Since your account can now define custom rules on what counts as a "valid" transaction and a valid "signature" by the signer, you can actually just have multiple signers and create a multi-sig wallet!

Your account can simply define a list of public keys related to all the signers, for example, and then not consider a signature valid until you have received signatures from all of them (or a majority subset, perhaps) before which it will not agree to execute the transaction.


On a similar note, we can now also build recoverable accounts. Your smart account can allow certain delegated signers, perhaps a secure hardware wallet address, to force reset the other signers linked to your account in case one, or some, of the signers get compromised providing you additional security over your wallet.

Other forms of access control can include being able to freeze your account through a delegated signer in case you suspect one of the signers has been compromised, or being able to restrict certain signers to only certain types of transactions, and so on.

Custom Signature Schemes

Ethereum, by default, comes with Ed25519 based signatures. Your public/private keys in an EOA are an Ed25519 keypair, and they are used to sign signatures compatible with the Ed25519 signature scheme.


However, since your new smart accounts are fully programmable, you can decide what signature scheme you'd like to use yourself! 

Perhaps you're paranoid and want to move over to a quantum-resistant signature scheme earlier than others. Perhaps you do not want to deal with any sort of keypairs at all and instead simply use a wallet that uses a pre-existing secure piece of data as the private key to sign transactions - for example, a mobile app that uses the iPhone's FaceID's cryptography algorithm to sign messages thereby not needing to maintain a keypair at all. 

In fact, you could even have an account that allows you to change the signature scheme you'd like to use. Perhaps currently you have an Android phone and add support for Android's TouchID equivalent, and in the future you get an iPhone and modify your account to now use FaceID compatible signatures.

On Ethereum, Account Abstraction has been implemented through EIP-4337. We will dive deeper and do a technical breakdown of EIP-4337 in the next lesson, and then in the last two lessons we will actually build projects that take advantage of account abstraction.

However, other blockchains have also worked to incorporate account abstraction on their own chains in their own manner. zkSync Era, for example, has account abstraction built into the protocol layer where there is no distinction between an EOA and a Smart Account - all accounts are in fact smart accounts. Similarly, Flow also has account abstraction built directly in the protocol layer.

Our focus throughout this series though will be look at Ethereum's implementation through EIP-4337 - which is also going to be relevant for most EVM compatible chains (like Polygon) and Ethereum Layer 2's (like Optimism, Arbitrum, Base, etc) which will have support for EIP-4337 as well.



