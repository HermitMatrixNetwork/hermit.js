<p align="center">
  <img src="./media/logo-light.svg#gh-light-mode-only" type="image/svg+xml" width="75%" class="light-logo" />
  <img src="./media/logo-dark.svg#gh-dark-mode-only" type="image/svg+xml" width="75%" class="dark-logo" />
</p>

<p align="center">
  The JavaScript SDK for Secret Network.
</p>

<p align="center">
  <img alt="npm" src="https://img.shields.io/npm/v/secretjs/beta" />
  <img alt="ci" style="margin-left: 0.3em" src="https://github.com/scrtlabs/secret.js/actions/workflows/test.yml/badge.svg?branch=master" />
</p>

<p align="center">
  <a href="https://secretjs.scrt.network" target="_blank"><strong>Explore the Docs »</strong></a>
</p>

<h1 id="table-of-contents">Table of Contents</h1>

- [Key Features](#key-features)
- [Beta Version Notice](#beta-version-notice)
- [Installation](#installation)
- [Usage Examples](#usage-examples)
  - [Sending Queries](#sending-queries)
  - [Broadcasting Transactions](#broadcasting-transactions)
- [API](#api)
  - [Wallet](#wallet)
    - [Importing account from mnemonic phrase](#importing-account-from-mnemonic-phrase)
    - [Generating a random account](#generating-a-random-account)
  - [SecretNetworkClient](#secretnetworkclient)
    - [Readonly](#readonly)
      - [`secretjs.query`](#secretjsquery)
    - [Signer](#signer)
      - [`secretjs.address`](#secretjsaddress)
      - [`secretjs.tx`](#secretjstx)
    - [Using Keplr](#using-keplr)
      - [`getOfflineSignerOnlyAmino()`](#getofflinesigneronlyamino)
      - [`getOfflineSigner()`](#getofflinesigner)
      - [`getOfflineSignerAuto()`](#getofflinesignerauto)
- [Migrating from Secret.js v0.17.x](#migrating-from-secretjs-v017x)

# Key Features

Secret.js a JavaScript SDK for writing applications that interact with the Secret Network blockchain.

- Written in TypeScript and provided with type definitions.
- Provides simple abstractions over core data structures.
- Supports every possible message and transaction type.
- Exposes every possible query type.
- Handles input/output encryption/decryption for Secret Contracts.
- Works in Node.js, modern web browsers and React Native.

# Beta Version Notice

This library is still in beta, **APIs may break**. Beta testers are welcome!

See [project board](https://github.com/scrtlabs/secret.js/projects/1) for list of existing/missing features.

# Installation

```bash
npm install secretjs@beta
```

or

```bash
yarn add secretjs@beta
```

# Usage Examples

For a lot more usage examples [refer to the tests](./test/test.ts).

## Sending Queries

```typescript
import { SecretNetworkClient } from "secretjs";

// To create a readonly secretjs client, just pass in an RPC endpoint
const secretjs = await SecretNetworkClient.create({
  rpcUrl: "https://rpc-secret.scrtlabs.com/secret-4/rpc/",
});

const {
  balance: { amount },
} = await secretjs.query.bank.balance({
  address: "secret1ap26qrlp8mcq2pg6r47w43l0y8zkqm8a450s03",
  denom: "uscrt",
});

console.log(`I have ${amount / 1e6} SCRT!`);

const sSCRT = "secret1k0jntykt7e4g3y88ltc60czgjuqdy4c9e8fzek";
// Get codeHash using `secretcli q compute contract-hash secret1k0jntykt7e4g3y88ltc60czgjuqdy4c9e8fzek`
const sScrtCodeHash =
  "af74387e276be8874f07bec3a87023ee49b0e7ebe08178c49d0a49c3c98ed60e";

const { token_info } = await secretjs.query.compute.queryContract({
  address: sSCRT,
  codeHash: sScrtCodeHash, // optional but way faster
  query: { token_info: {} },
});

console.log(`sSCRT has ${token_info.decimals} decimals!`);
```

## Broadcasting Transactions

```typescript
import { Wallet, SecretNetworkClient, MsgSend, MsgMultiSend } from "secretjs";

const wallet = new Wallet(
  "grant rice replace explain federal release fix clever romance raise often wild taxi quarter soccer fiber love must tape steak together observe swap guitar",
);
const myAddress = wallet.address;

// To create a signer secretjs client, also pass in a wallet
const secretjs = await SecretNetworkClient.create({
  rpcUrl: "https://rpc.pulsar.griptapejs.com/",
  wallet: wallet,
  walletAddress: myAddress,
  chainId: "pulsar-2",
});

const bob = "secret1dgqnta7fwjj6x9kusyz7n8vpl73l7wsm0gaamk";
const msg = new MsgSend({
  fromAddress: myAddress,
  toAddress: bob,
  amount: [{ denom: "uscrt", amount: "1" }],
});

const tx = await secretjs.tx.broadcast([msg], {
  gasLimit: 20_000,
  gasPriceInFeeDenom: 0.25,
  feeDenom: "uscrt",
});
```

# API

## Wallet

An offline wallet implementation, used to sign transactions. Usually we'd just want to pass it to `SecretNetworkClient`.

[**Full API »**](https://secretjs.scrt.network/classes/Wallet.html)

### Importing account from mnemonic phrase

```typescript
import { Wallet } from "secretjs";

const wallet = new Wallet(
  "grant rice replace explain federal release fix clever romance raise often wild taxi quarter soccer fiber love must tape steak together observe swap guitar",
);
const myAddress = wallet.address;
```

### Generating a random account

```typescript
import { Wallet } from "secretjs";

const wallet = new Wallet();
const myAddress = wallet.address;
const myMnemonicPhrase = wallet.mnemonic;
```

## SecretNetworkClient

[**Full API »**](https://secretjs.scrt.network/classes/SecretNetworkClient.html)

### Readonly

A readonly client can only send queries and get chain information.

#### `secretjs.query`

You can access all query types using `secretjs.query`:

| Query                                                       | Docs              |
| ----------------------------------------------------------- | ----------------- |
| `secretjs.query.getTx(hash)`                                | TODO link to docs |
| `secretjs.query.txsQuery(query)`                            | TODO link to docs |
| `secretjs.query.auth.account()`                             | TODO link to docs |
| `secretjs.query.auth.accounts()`                            | TODO link to docs |
| `secretjs.query.auth.params()`                              | TODO link to docs |
| `secretjs.query.authz.grants()`                             | TODO link to docs |
| `secretjs.query.bank.balance()`                             | TODO link to docs |
| `secretjs.query.bank.allBalances()`                         | TODO link to docs |
| `secretjs.query.bank.totalSupply()`                         | TODO link to docs |
| `secretjs.query.bank.supplyOf()`                            | TODO link to docs |
| `secretjs.query.bank.params()`                              | TODO link to docs |
| `secretjs.query.bank.denomMetadata()`                       | TODO link to docs |
| `secretjs.query.bank.denomsMetadata()`                      | TODO link to docs |
| `secretjs.query.compute.contractCodeHash()`                 | TODO link to docs |
| `secretjs.query.compute.codeHash()`                         | TODO link to docs |
| `secretjs.query.compute.contractInfo()`                     | TODO link to docs |
| `secretjs.query.compute.contractsByCode()`                  | TODO link to docs |
| `secretjs.query.compute.queryContract()`                    | TODO link to docs |
| `secretjs.query.compute.code()`                             | TODO link to docs |
| `secretjs.query.compute.codes()`                            | TODO link to docs |
| `secretjs.query.distribution.params()`                      | TODO link to docs |
| `secretjs.query.distribution.validatorOutstandingRewards()` | TODO link to docs |
| `secretjs.query.distribution.validatorCommission()`         | TODO link to docs |
| `secretjs.query.distribution.validatorSlashes()`            | TODO link to docs |
| `secretjs.query.distribution.delegationRewards()`           | TODO link to docs |
| `secretjs.query.distribution.delegationTotalRewards()`      | TODO link to docs |
| `secretjs.query.distribution.delegatorValidators()`         | TODO link to docs |
| `secretjs.query.distribution.delegatorWithdrawAddress()`    | TODO link to docs |
| `secretjs.query.distribution.communityPool()`               | TODO link to docs |
| `secretjs.query.distribution.foundationTax()`               | TODO link to docs |
| `secretjs.query.evidence.evidence()`                        | TODO link to docs |
| `secretjs.query.evidence.allEvidence()`                     | TODO link to docs |
| `secretjs.query.feegrant.allowance()`                       | TODO link to docs |
| `secretjs.query.feegrant.allowances()`                      | TODO link to docs |
| `secretjs.query.gov.proposal()`                             | TODO link to docs |
| `secretjs.query.gov.proposals()`                            | TODO link to docs |
| `secretjs.query.gov.vote()`                                 | TODO link to docs |
| `secretjs.query.gov.votes()`                                | TODO link to docs |
| `secretjs.query.gov.params()`                               | TODO link to docs |
| `secretjs.query.gov.deposit()`                              | TODO link to docs |
| `secretjs.query.gov.deposits()`                             | TODO link to docs |
| `secretjs.query.gov.tallyResult()`                          | TODO link to docs |
| `secretjs.query.ibc_channel.channel()`                      | TODO link to docs |
| `secretjs.query.ibc_channel.channels()`                     | TODO link to docs |
| `secretjs.query.ibc_channel.connectionChannels()`           | TODO link to docs |
| `secretjs.query.ibc_channel.channelClientState()`           | TODO link to docs |
| `secretjs.query.ibc_channel.channelConsensusState()`        | TODO link to docs |
| `secretjs.query.ibc_channel.packetCommitment()`             | TODO link to docs |
| `secretjs.query.ibc_channel.packetCommitments()`            | TODO link to docs |
| `secretjs.query.ibc_channel.packetReceipt()`                | TODO link to docs |
| `secretjs.query.ibc_channel.packetAcknowledgement()`        | TODO link to docs |
| `secretjs.query.ibc_channel.packetAcknowledgements()`       | TODO link to docs |
| `secretjs.query.ibc_channel.unreceivedPackets()`            | TODO link to docs |
| `secretjs.query.ibc_channel.unreceivedAcks()`               | TODO link to docs |
| `secretjs.query.ibc_channel.nextSequenceReceive()`          | TODO link to docs |
| `secretjs.query.ibc_client.clientState()`                   | TODO link to docs |
| `secretjs.query.ibc_client.clientStates()`                  | TODO link to docs |
| `secretjs.query.ibc_client.consensusState()`                | TODO link to docs |
| `secretjs.query.ibc_client.consensusStates()`               | TODO link to docs |
| `secretjs.query.ibc_client.clientStatus()`                  | TODO link to docs |
| `secretjs.query.ibc_client.clientParams()`                  | TODO link to docs |
| `secretjs.query.ibc_client.upgradedClientState()`           | TODO link to docs |
| `secretjs.query.ibc_client.upgradedConsensusState()`        | TODO link to docs |
| `secretjs.query.ibc_connection.connection()`                | TODO link to docs |
| `secretjs.query.ibc_connection.connections()`               | TODO link to docs |
| `secretjs.query.ibc_connection.clientConnections()`         | TODO link to docs |
| `secretjs.query.ibc_connection.connectionClientState()`     | TODO link to docs |
| `secretjs.query.ibc_connection.connectionConsensusState()`  | TODO link to docs |
| `secretjs.query.ibc_transfer.denomTrace()`                  | TODO link to docs |
| `secretjs.query.ibc_transfer.denomTraces()`                 | TODO link to docs |
| `secretjs.query.ibc_transfer.params()`                      | TODO link to docs |
| `secretjs.query.mint.params()`                              | TODO link to docs |
| `secretjs.query.mint.inflation()`                           | TODO link to docs |
| `secretjs.query.mint.annualProvisions()`                    | TODO link to docs |
| `secretjs.query.params.params()`                            | TODO link to docs |
| `secretjs.query.registration.txKey()`                       | TODO link to docs |
| `secretjs.query.registration.registrationKey()`             | TODO link to docs |
| `secretjs.query.registration.encryptedSeed()`               | TODO link to docs |
| `secretjs.query.slashing.params()`                          | TODO link to docs |
| `secretjs.query.slashing.signingInfo()`                     | TODO link to docs |
| `secretjs.query.slashing.signingInfos()`                    | TODO link to docs |
| `secretjs.query.staking.validators()`                       | TODO link to docs |
| `secretjs.query.staking.validator()`                        | TODO link to docs |
| `secretjs.query.staking.validatorDelegations()`             | TODO link to docs |
| `secretjs.query.staking.validatorUnbondingDelegations()`    | TODO link to docs |
| `secretjs.query.staking.delegation()`                       | TODO link to docs |
| `secretjs.query.staking.unbondingDelegation()`              | TODO link to docs |
| `secretjs.query.staking.delegatorDelegations()`             | TODO link to docs |
| `secretjs.query.staking.delegatorUnbondingDelegations()`    | TODO link to docs |
| `secretjs.query.staking.redelegations()`                    | TODO link to docs |
| `secretjs.query.staking.delegatorValidators()`              | TODO link to docs |
| `secretjs.query.staking.delegatorValidator()`               | TODO link to docs |
| `secretjs.query.staking.historicalInfo()`                   | TODO link to docs |
| `secretjs.query.staking.pool()`                             | TODO link to docs |
| `secretjs.query.staking.params()`                           | TODO link to docs |
| `secretjs.query.tendermint.getNodeInfo()`                   | TODO link to docs |
| `secretjs.query.tendermint.getSyncing()`                    | TODO link to docs |
| `secretjs.query.tendermint.getLatestBlock()`                | TODO link to docs |
| `secretjs.query.tendermint.getBlockByHeight()`              | TODO link to docs |
| `secretjs.query.tendermint.getLatestValidatorSet()`         | TODO link to docs |
| `secretjs.query.tendermint.getValidatorSetByHeight()`       | TODO link to docs |
| `secretjs.query.upgrade.currentPlan()`                      | TODO link to docs |
| `secretjs.query.upgrade.appliedPlan()`                      | TODO link to docs |
| `secretjs.query.upgrade.upgradedConsensusState()`           | TODO link to docs |
| `secretjs.query.upgrade.moduleVersions()`                   | TODO link to docs |

### Signer

A signer client can broadcast transactions, send queries and get chain information.

In addition to using `secretjs.query`, you can also use `secretjs.tx` & `secretjs.address`.

#### `secretjs.address`

`secretjs.address` is the same as `walletAddress`:

```typescript
import { Wallet, SecretNetworkClient, MsgSend, MsgMultiSend } from "secretjs";

const wallet = new Wallet(
  "grant rice replace explain federal release fix clever romance raise often wild taxi quarter soccer fiber love must tape steak together observe swap guitar",
);
const myAddress = wallet.address;

// To create a signer secretjs client, also pass in a wallet
const secretjs = await SecretNetworkClient.create({
  rpcUrl: "https://rpc.pulsar.griptapejs.com/",
  wallet: wallet,
  walletAddress: myAddress,
  chainId: "pulsar-2",
});

const alsoMyAddress = secretjs.address;
```

#### `secretjs.tx`

TODO

### Using Keplr

The recommended way is using `window.keplr.getOfflineSignerOnlyAmino()`:

```typescript
const sleep = (ms: number) => new Promise((resolve) => setTimeout(resolve, ms));

while (
  !window.keplr ||
  !window.getEnigmaUtils ||
  !window.getOfflineSignerOnlyAmino
) {
  await sleep(50);
}

const CHAIN_ID = "secret-4";
const SECRET_RPC = "https://rpc-secret.scrtlabs.com/secret-4/rpc/";

await window.keplr.enable(CHAIN_ID);

const keplrOfflineSigner = window.getOfflineSignerOnlyAmino(CHAIN_ID);
const [{ address: myAddress }] = await keplrOfflineSigner.getAccounts();

const secretjs = await SecretNetworkClient.create({
  rpcUrl: SECRET_RPC,
  chainId: SECRET_CHAIN_ID,
  wallet: keplrOfflineSigner,
  walletAddress: myAddress,
  encryptionUtils: window.getEnigmaUtils(SECRET_CHAIN_ID),
});

// Note: Using `window.getEnigmaUtils` is optional, it will allow
// Keplr to use the same encryption seed across sessions for the account.
// The benefit of this is that `secretjs.query.getTx()` will be able to decrypt
// the response across sessions.
```

#### `getOfflineSignerOnlyAmino()`

Although this is the legacy way of signing transactions on cosmos-sdk, it's still the most recommended due to Ledger support & better UI on Keplr.

- 🟩 Looks good on Keplr
- 🟩 Supports users signing with Ledger
- 🟥 Doesn't support signing transactions with these Msgs:
  - [authz/MsgExec](https://secretjs.scrt.network/classes/MsgExec)
  - [authz/MsgGrant](https://secretjs.scrt.network/classes/MsgGrant)
  - [authz/MsgRevoke](https://secretjs.scrt.network/classes/MsgRevoke)
  - [feegrant/MsgGrantAllowance](https://secretjs.scrt.network/classes/MsgGrantAllowance)
  - [feegrant/MsgRevokeAllowance](https://secretjs.scrt.network/classes/MsgRevokeAllowance)
  - All IBC relayer Msgs:
    - [gov/MsgSubmitProposal/ClientUpdateProposal](https://secretjs.scrt.network/enums/ProposalType#ClientUpdateProposal)
    - [gov/MsgSubmitProposal/UpgradeProposal](https://secretjs.scrt.network/enums/ProposalType#UpgradeProposal)
    - [ibc_channel/MsgAcknowledgement](https://secretjs.scrt.network/classes/MsgAcknowledgement)
    - [ibc_channel/MsgChannelCloseConfirm](https://secretjs.scrt.network/classes/MsgChannelCloseConfirm)
    - [ibc_channel/MsgChannelCloseInit](https://secretjs.scrt.network/classes/MsgChannelCloseInit)
    - [ibc_channel/MsgChannelOpenAck](https://secretjs.scrt.network/classes/MsgChannelOpenAck)
    - [ibc_channel/MsgChannelOpenConfirm](https://secretjs.scrt.network/classes/MsgChannelOpenConfirm)
    - [ibc_channel/MsgChannelOpenInit](https://secretjs.scrt.network/classes/MsgChannelOpenInit)
    - [ibc_channel/MsgChannelOpenTry](https://secretjs.scrt.network/classes/MsgChannelOpenTry)
    - [ibc_channel/MsgRecvPacket](https://secretjs.scrt.network/classes/MsgRecvPacket)
    - [ibc_channel/MsgTimeout](https://secretjs.scrt.network/classes/MsgTimeout)
    - [ibc_channel/MsgTimeoutOnClose](https://secretjs.scrt.network/classes/MsgTimeoutOnClose)
    - [ibc_client/MsgCreateClient](https://secretjs.scrt.network/classes/MsgCreateClient)
    - [ibc_client/MsgSubmitMisbehaviour](https://secretjs.scrt.network/classes/MsgSubmitMisbehaviour)
    - [ibc_client/MsgUpdateClient](https://secretjs.scrt.network/classes/MsgUpdateClient)
    - [ibc_client/MsgUpgradeClient](https://secretjs.scrt.network/classes/MsgUpgradeClient)
    - [ibc_connection/MsgConnectionOpenAck](https://secretjs.scrt.network/classes/MsgConnectionOpenAck)
    - [ibc_connection/MsgConnectionOpenConfirm](https://secretjs.scrt.network/classes/MsgConnectionOpenConfirm)
    - [ibc_connection/MsgConnectionOpenInit](https://secretjs.scrt.network/classes/MsgConnectionOpenInit)
    - [ibc_connection/MsgConnectionOpenTry](https://secretjs.scrt.network/classes/MsgConnectionOpenTry)

Please note that [ibc_transfer/MsgTransfer](https://secretjs.scrt.network/classes/MsgTransfer) for sending funds across IBC is supported.

<img src="./media/keplr-amino.png" width="45%" />

#### `getOfflineSigner()`

The new way of signing transactions on cosmos-sdk, it's more efficient but still doesn't have Ledger support, so it's most recommended for usage in apps that don't require signing transactions with Ledger.

- 🟥 Looks bad on Keplr
- 🟥 Doesn't support users signing with Ledger
- 🟩 Supports signing transactions with all types of Msgs

<img src="./media/keplr-proto.png" width="45%" />

#### `getOfflineSignerAuto()`

Currently this is equivalent to `keplr.getOfflineSigner()` but may change at the discretion of the Keplr team.

# Migrating from Secret.js v0.17.x

TODO
