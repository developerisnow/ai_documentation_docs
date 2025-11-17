# Direct indexing
https://docs.goldsky.com/mirror/sources/direct-indexing
With mirror pipelines, you can access to indexed on-chain data. Define them as a source and pipe them into any sink we support.

## Use-cases

* Mirror specific logs and traces from a set of contracts into a postgres database to build an API for your protocol
* ETL data into a data warehouse to run analytics
* Push the full blockchain into Kafka or S3 to build a datalake for ML

## Supported Chains

### EVM chains

For EVM chains we support the following 4 datasets:

| Dataset               | Description                                                                                                                        |
| --------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| Blocks                | Metadata for each block on the chain including hashes, transaction count, difficulty, and gas used.                                |
| Logs                  | Raw logs for events emitted from contracts. Contains the contract address, data, topics, and metadata for blocks and transactions. |
| Enriched Transactions | Transaction data including input, value, from and to address, and metadata for the block, gas, and receipts.                       |
| Traces                | Traces of all function calls made on the chain including metadata for block, trace, transaction, and gas.                          |

### Fast Scan

Some datasets have support for [Fast Scan](/mirror/sources/direct-indexing#backfill-vs-fast-scan) which allows you to more quickly backfill filtered data. If a chain has partial support for Fast Scan, the dataset that doesn't support fast scan will have an asterisk `*` next to it.

Here's a breakdown of the EVM chains we support and their corresponding datasets:

|                      | Blocks | Enriched Transactions | Logs | Traces | Fast Scan |
| -------------------- | ------ | --------------------- | ---- | ------ | --------- |
| 0G                   | ✓      | ✓                     | ✓    | ✗      | ✗         |
| 0G Galileo Testnet   | ✓      | ✓                     | ✓    | ✗      | ✗         |
| Abstract             | ✓      | ✓                     | ✓    | ✗      | ✗         |
| Align Testnet        | ✓      | ✓                     | ✓    | ✓      | ✓         |
| Apechain             | ✓      | ✓                     | ✓    | ✗      | ✗         |
| Apechain Curtis      | ✓      | ✓                     | ✓    | ✓\*    | ✓         |
| Proof of Play Apex   | ✓      | ✓                     | ✓    | ✗      | ✓         |
| Arbitrum Nova        | ✓      | ✓                     | ✓    | ✗      | ✓         |
| Arbitrum One         | ✓      | ✓                     | ✓    | ✓\*    | ✓         |
| Arbitrum Sepolia     | ✓      | ✓                     | ✓    | ✗      | ✓         |
| Arena-Z              | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Arena-Z Testnet      | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Arweave \*           | ✓      | ✓                     | N/A  | N/A    | ✗         |
| Automata             | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Automata Testnet     | ✓      | ✓                     | ✓    | ✗      | ✗         |
| Avalanche            | ✓      | ✓                     | ✓    | ✓      | ✓         |
| B3                   | ✓      | ✓                     | ✓    | ✓      | ✗         |
| B3 Sepolia           | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Base                 | ✓      | ✓                     | ✓    | ✓      | ✓         |
| Base Sepolia         | ✓      | ✓                     | ✓    | ✓      | ✓         |
| Berachain Bepolia    | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Berachain Mainnet    | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Bitcoin              | ✓      | ✓                     | ✗    | ✗      | ✗         |
| Blast                | ✓      | ✓                     | ✓    | ✓      | ✓         |
| Build on Bitcoin     | ✓      | ✓                     | ✓    | ✓      | ✓         |
| Binance Smart Chain  | ✓      | ✓                     | ✓    | ✓      | ✓         |
| Camp Testnet         | ✓      | ✓                     | ✓    | ✓      | ✓         |
| Celo                 | ✓\*    | ✓\*                   | ✓    | ✓      | ✓         |
| Celo Dango Testnet   | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Codex                | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Corn Maizenet        | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Cronos zkEVM         | ✓      | ✓                     | ✓    | ✗      | ✗         |
| Cronos zkEVM Sepolia | ✓      | ✓                     | ✓    | ✗      | ✗         |
| Cyber                | ✓      | ✓                     | ✓    | ✓      | ✓         |
| Cyber Testnet        | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Degen                | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Ethena Testnet       | ✓      | ✓                     | ✓    | ✗      | ✗         |
| Ethereum             | ✓      | ✓                     | ✓    | ✓      | ✓         |
| Ethereum Holesky     | ✓      | ✓                     | ✓    | ✓      | ✓         |
| Ethereum Sepolia     | ✓      | ✓                     | ✓    | ✓      | ✓         |
| Etherlink            | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Etherlink Testnet    | ✓      | ✓                     | ✓    | ✗      | ✗         |
| Ethernity            | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Ethernity Testnet    | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Fantom               | ✓      | ✓                     | ✓    | ✓      | ✓         |
| Flare                | ✓      | ✓                     | ✓    | ✗      | ✗         |
| Flare Testnet        | ✓      | ✓                     | ✓    | ✗      | ✗         |
| Fluent Devnet        | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Forma                | ✓      | ✓                     | ✓    | ✓      | ✓         |
| Frax                 | ✓      | ✓                     | ✓    | ✓      | ✓         |
| Gnosis               | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Gravity              | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Ham                  | ✓      | ✓                     | ✓    | ✓      | ✗         |
| HashKey              | ✓      | ✓                     | ✓    | ✗      | ✗         |
| HyperEVM             | ✓      | ✓                     | ✓    | ✗      | ✗         |
| Immutable Testnet    | ✓      | ✓                     | ✓    | ✗      | ✗         |
| Immutable zkEVM      | ✓      | ✓                     | ✓    | ✗      | ✗         |
| Ink                  | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Ink Sepolia          | ✓      | ✓                     | ✓    | ✓      | ✗         |
| IOTA EVM             | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Kroma                | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Linea                | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Lisk                 | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Lisk Sepolia         | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Lith Testnet         | ✓      | ✓                     | ✓    | ✓      | ✓         |
| Lyra                 | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Lyra Testnet         | ✓      | ✓                     | ✓    | ✓      | ✗         |
| MegaETH Testnet      | ✓      | ✓                     | ✓    | ✗      | ✗         |
| Metal                | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Metal Testnet        | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Mezo                 | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Mezo Testnet         | ✓      | ✓                     | ✓    | ✗      | ✗         |
| Midnight Devnet      | ✓      | ✓                     | ✓    | ✗      | ✗         |
| Mint                 | ✓      | ✓                     | ✓    | ✓      | ✓         |
| Mint Sepolia         | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Mode                 | ✓      | ✓                     | ✓    | ✓      | ✓         |
| Mode Testnet         | ✓      | ✓                     | ✓    | ✓      | ✓         |
| Monad Testnet        | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Morph                | ✓      | ✓                     | ✓    | ✗      | ✗         |
| Neura Testnet        | ✓      | ✓                     | ✓    | ✗      | ✗         |
| Oasys Homeverse      | ✓      | ✓                     | ✓    | ✓\*    | ✓         |
| Optimism             | ✓      | ✓                     | ✓    | ✓      | ✓         |
| Optimism Sepolia     | ✓      | ✓                     | ✓    | ✓      | ✓         |
| Orderly              | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Orderly Sepolia      | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Palm                 | ✓      | ✓                     | ✓    | ✓      | ✓         |
| Palm Testnet         | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Plasma               | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Plasma Testnet       | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Plume                | ✓      | ✓                     | ✓    | ✗      | ✗         |
| Pharos Devnet        | ✓      | ✓                     | ✓    | ✗      | ✗         |
| Pharos Testnet       | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Polygon              | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Polynomial           | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Proof of Play Barret | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Proof of Play Boss   | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Proof of Play Cloud  | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Public Good Network  | ✓      | ✓                     | ✓    | ✓      | ✓         |
| Race                 | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Rari                 | ✓      | ✓                     | ✓    | ✓      | ✓         |
| Redstone             | ✓      | ✓                     | ✓    | ✓      | ✓         |
| Reya                 | ✓      | ✓                     | ✓    | ✗      | ✓         |
| Rise Sepolia         | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Ruby Testnet         | ✓      | ✓                     | ✓    | ✗      | ✓         |
| Scroll               | ✓      | ✓                     | ✓    | ✓      | ✓         |
| Scroll Sepolia       | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Sei                  | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Settlus              | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Shape                | ✓      | ✓                     | ✓    | ✓      | ✓         |
| Shape Sepolia        | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Shrapnel             | ✓      | ✓                     | ✓    | ✗      | ✓         |
| SNAXchain            | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Soneium              | ✓      | ✓                     | ✓    | ✗      | ✗         |
| Soneium Minato       | ✓      | ✓                     | ✓    | ✗      | ✗         |
| Sonic                | ✓      | ✓                     | ✓    | ✗      | ✗         |
| Sophon               | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Sophon Testnet       | ✓      | ✓                     | ✓    | ✗      | ✗         |
| Story                | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Story Aeneid Testnet | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Superseed            | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Superseed Sepolia    | ✓      | ✓                     | ✓    | ✓      | ✓         |
| Swan                 | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Swellchain           | ✓      | ✓                     | ✓    | ✗      | ✗         |
| Swellchain Testnet   | ✓      | ✓                     | ✓    | ✗      | ✗         |
| TAC                  | ✓      | ✓                     | ✓    | ✗      | ✗         |
| TAC Turin Testnet    | ✓      | ✓                     | ✓    | ✗      | ✗         |
| Taiko Hoodi Testnet  | ✓      | ✓                     | ✓    | ✗      | ✗         |
| TRON                 | ✓      | ✓                     | ✓    | ✗      | ✗         |
| Unichain             | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Unichain Sepolia     | ✓      | ✓                     | ✓    | ✗      | ✗         |
| Viction              | ✓      | ✓                     | ✓    | ✗      | ✗         |
| World Chain          | ✓      | ✓                     | ✓    | ✗      | ✗         |
| XPLA                 | ✓      | ✓                     | ✓    | ✗      | ✗         |
| XR Sepolia           | ✓      | ✓                     | ✓    | ✗      | ✗         |
| Xterio               | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Zero                 | ✓      | ✓                     | ✓    | ✓      | ✗         |
| Zero Sepolia         | ✓      | ✓                     | ✓    | ✗      | ✗         |
| Zetachain            | ✓      | ✓                     | ✓    | ✓      | ✓         |
| zkSync Era           | ✓      | ✓                     | ✓    | ✓      | ✓         |
| Zora                 | ✓      | ✓                     | ✓    | ✓      | ✓         |
| Zora Sepolia         | ✓      | ✓                     | ✓    | ✓      | ✗         |

\* The Arweave dataset includes bundled/L2 data.

### Non-EVM chains

#### Beacon

| Dataset                                    | Description                                                                         |
| ------------------------------------------ | ----------------------------------------------------------------------------------- |
| Attestations                               | Attestations (votes) from validators for the block.                                 |
| Attester Slashing                          | Metadata for attester slashing.                                                     |
| Blocks                                     | Metadata for each block on the chain including hashes, deposit count, and gas used. |
| BLS Signature to Execution Address Changes | BLS Signature to Execution Address Changes.                                         |
| Deposits                                   | Metadata for deposits.                                                              |
| Proposer Slashing                          | Metadata for proposer slashing.                                                     |
| Voluntary Exits                            | Metadata for voluntary exits.                                                       |
| Withdrawls                                 | Metadata for withdrawls.                                                            |

#### Fogo

| Dataset                        | Description                                                                                              |
| ------------------------------ | -------------------------------------------------------------------------------------------------------- |
| Transactions with Instructions | Enriched transaction data including instructions, accounts, balance changes, and metadata for the block. |
| Rewards                        | Records of rewards distributed to validators for securing and validating the network.                    |
| Blocks                         | Metadata for each block on the chain including hashes, transaction count, slot and leader rewards.       |

#### IOTA

| Dataset      | Description                                                                                                                                                                                                  |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Checkpoints  | A checkpoint is a periodic, finalized snapshot of the blockchain's state in the Movement VM, batching transactions to ensure consistency and scalability across the network.                                 |
| Epochs       | An epoch is a defined time period in the Movement VM during which a fixed set of validators processes transactions and manages governance, with transitions enabling validator rotation and network updates. |
| Events       | Events in the Movement VM are structured data emissions from smart contracts, recorded on the blockchain to log significant actions or state changes for external monitoring and interaction.                |
| Move Calls   | Move calls are a function invocation within a Move smart contract, executed by the Movement VM to perform specific operations or state transitions on the blockchain.                                        |
| Transactions | A transaction in the Movement VM is a signed instruction executed by the Move smart contract to modify the blockchain's state, such as transferring assets or invoking contract functions.                   |

#### Movement

| Dataset                     | Description                                                                                                |
| --------------------------- | ---------------------------------------------------------------------------------------------------------- |
| Account Transactions        | All raw onchain transactions involving account-level actions (e.g., transaction version, account address). |
| Block Metadata Transactions | Metadata about blocks and block-level transactions (e.g., block height, epoch, version).                   |
| Fungible Asset Balances     | Real-time balances of fungible tokens across accounts.                                                     |
| Current Token Data          | Latest metadata for tokens - includes name, description, supply, etc.                                      |
| Current Token Ownerships    | Snapshot of token ownership across the chain.                                                              |
| Events                      | All emitted contract event logs - useful for indexing arbitrary contract behavior.                         |
| Fungible Asset Activities   | Track activity for fungible tokens - owner address, amount, and type.                                      |
| Fungible Asset Balances     | Historical balance tracking for fungible assets (not just the current state).                              |
| Fungible Asset Metadata     | Static metadata for fungible tokens - like decimals, symbol, and name.                                     |
| Signatures                  | Cryptographic signature data from transactions, useful for validating sender authenticity.                 |
| Token Activities            | Detailed logs of token movements and interactions across tokens and NFTs.                                  |

#### Solana

| Dataset                             | Description                                                                                                                                                      |
| ----------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Edge Accounts                       | Contains details of all active accounts on the Solana blockchain, including balance and owner information. Live data from slot 271611201.                        |
| Edge Blocks                         | Metadata for each block on the chain including hashes, transaction count, difficulty, and gas used. Live data from slot 271611201.                               |
| Edge Instructions                   | Specific operations within transactions that describe the actions to be performed on the Solana blockchain. Live data from slot 271611201.                       |
| Edge Rewards                        | Records of rewards distributed to validators for securing and validating the Solana network. Live data from slot 271611201.                                      |
| Edge Token Transfers                | Transactions involving the movement of tokens between accounts on the Solana blockchain. Live data from slot 271611201.                                          |
| Edge Tokens                         | Information about different token types issued on the Solana blockchain, including metadata and supply details. Live data from slot 271611201.                   |
| Edge Transactions                   | Enriched transaction data including input, value, from and to address, and metadata for the block, gas and receipt. Live data from slot 271611201.               |
| Edge Transactions with Instructions | Enriched transaction data including instructions, input, value, from and to address, and metadata for the block, gas and receipt. Live data from slot 316536533. |

<Note>
  You can interact with these Solana datasets at no cost at
  [https://crypto.clickhouse.com/](https://crypto.clickhouse.com/)
</Note>

#### Starknet

| Dataset      | Description                                                                                                  |
| ------------ | ------------------------------------------------------------------------------------------------------------ |
| Blocks       | Metadata for each block on the chain including hashes, transaction count, difficulty, and gas used.          |
| Events       | Consists of raw event data from the blockchain, documenting various on-chain activities and triggers.        |
| Messages     | Messaging data from the Starknet blockchain, used for L2 & L1 communication.                                 |
| Transactions | Transaction data including input, value, from and to address, and metadata for the block, gas, and receipts. |

#### Stellar

| Dataset         | Description                                                                                                                                                         |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Assets          | Contains information about all assets issued on the Stellar network, including details like asset codes, issuers, and related metadata.                             |
| Contract Events | Records events related to smart contract execution on the Stellar network, detailing the interactions and state changes within contracts.                           |
| Effects         | Captures the effects of various operations on the Stellar ledger, such as changes in balances, creation of accounts, and other state modifications.                 |
| Ledgers         | Provides a comprehensive record of all ledger entries, summarizing the state of the blockchain at each ledger close, including transaction sets and ledger headers. |

#### Sui

| Dataset      | Description                                                                                                                           |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------- |
| Checkpoints  | Contains raw data of blockchain checkpoints capturing the state of the ledger at specific intervals.                                  |
| Epochs       | Includes raw data detailing the various epochs in the blockchain, which mark significant periods or phases in the network's operation |
| Events       | Consists of raw event data from the blockchain, documenting various on-chain activities and triggers                                  |
| Packages     | Contains raw data about the deployed smart contract packages on the blockchain                                                        |
| Transactions | Transaction data including effects, events, senders, recipients, balance and object changes, and other metadata.                      |

### Curated Datasets

Beyond onchain datasets, the Goldsky team continuosly curates and publishes derived datasets that serve a specific audience or use case. Here's the list:

#### Token Transfers

You can expect every EVM chain to have the following datasets available:

| Dataset   | Description                                       |
| --------- | ------------------------------------------------- |
| ERC\_20   | Every transfer event for all fungible tokens.     |
| ERC\_721  | Every transfer event for all non-fungible tokens. |
| ERC\_1155 | Every transfer event for all ERC-1155 tokens.     |

#### Polymarket datasets

| Dataset              | Description                                                                                                                                                                                                                                                                                                                                        |
| -------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Global Open Interest | Keeps track of global open interest.                                                                                                                                                                                                                                                                                                               |
| Market Open Interest | Keeps track of open interest for each market.                                                                                                                                                                                                                                                                                                      |
| Order Filled         | This event is emitted when a single Polymarket order is partially or completely filled. For example: a 50c YES buy for 100 YES matched against a 50c YES sell for 100 YES will emit 2 Orderi Filled events, from the perspective of the YES buy and of the YES sell. This is useful for granular tracking of trading activity and history.         |
| Orders Matched       | This event is emitted when a Polymarket taker order is matched against a set of Polymarket maker(limit) orders. For example: a 50c YES buy for 200 YES matched against 2 50c YES sells for 100 YES each will emit a single Orders Matched event. Orders Matched gives a more high level view of trading activity as it only tracks taker activity. |
| User Balances        | This event keeps track of all user outcome token positions.                                                                                                                                                                                                                                                                                        |
| User Positions       | Keeps track of outcome token positions along with pnl specific data including average price and realized pnl.                                                                                                                                                                                                                                      |

Additional chains, including roll-ups, can be indexed on demand. Contact us at [sales@goldsky.com](mailto:sales@goldsky.com) to learn more.

## Schema

The schema for each of these datasets can be found [here](/reference/schema/EVM-schemas).

## Backfill vs Fast Scan

Goldsky allows you either backfill the entire datasets or alternatively pre-filter the data based on specific attributes.
This allows for an optimal cost and time efficient streaming experience based on your specific use case.

For more information on how to enable each streaming mode in your pipelines visit our [reference documentation](/reference/config-file/pipeline#backfill-vs-fast-scan).
