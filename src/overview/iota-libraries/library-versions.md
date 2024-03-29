# Library Versions

---

DEPRECATED - please refer to chapter [IOTA SDK and identity.rs](./iota-sdk.md)

This chapter focuses heavily on deprecated libraries like iota.rs and wallet.rs. Additionally, it addresses two different protocol versions: Chrysalis and Stardust, which were only relevant at the time of writing mid 2023. If you're new here, the information provided is outdated.

---

When configuring dependencies, it's crucial to consider the compatibility between different libraries and their versions, as well as their support for specific target platforms.

While some third-party crates like OpenSSL and RustLS may have mutually exclusive usage, this isn't a concern with the IOTA libraries. Instead, the key focus lies in addressing the following two questions:

1. Which versions are designated for Stardust, the current protocol version of the IOTA mainnet and Shimmer network, and which versions are intended for Chrysalis, the outdated protocol version of the IOTA mainnet?

2. Which versions can be successfully cross-compiled for the respective platform targets, including iOS, Android, and macOS?

## Stardust and Chrysalis

Stardust and Chrysalis are both IOTA _protocol_ versions.

The first question (see above) can be relatively straightforward to address.

### Current Library: IOTA SDK

Just use the current version of iota-sdk. It's compatible to Stardust, the current protocol on both Shimmer Network and IOTA Mainnet.

### Deprecated Libraries: iota-client.rs / wallet.rs

To support both current networks, the deprecated libraries' versions used should start with major version 2, such as 2.0.1.rc-7. _As a developer, you can verify this by checking the Cargo.lock file_.

> **Why?**
>
> Browse the list of TIPs, and you will note that every item is tagged with Chrysalis (outdated) or Stardust.
>
> <a href="https://github.com/iotaledger/tips#list-of-tips" target="_blank">👉 &nbsp; The list of TIPs</a>
>
> TIP-0013 is labeled as _Chrysalis_, and it is associated with the REST API, which includes API calls using `api/v1`. These requests are utilized in version 1.4.0.
>
> TIP-0025 is labeled as _Stardust_, and it is associated with the Core REST API that involves API calls using `api/v2`. These requests are utilized in versions 2.x.y.

## Versions used for "full-featured" app - based on the deprecated libraries iota.rs and wallet.rs

The second question (see above) is more complex. It primarily revolves around determining which third-party libraries are used by the IOTA libraries and what dependencies are employed by those libraries, and so on.

Because each additional library increases the risk that it may not be cross-compiled for a specific target platform (iOS, Android, etc.). I specifically want to mention the two libraries, _libsodium_ and _rocksdb_, at this point. Unfortunately, there are often issues when compiling for different targets with these libraries.

---

Here is a matrix illustrating the library versions utilized in a "full-featured" Stardust app. The objective is to employ iota-client, iota-wallet, and identity_iota simultaneously, along with the stronghold feature.

**Status as of Jan 2023**

<table style="display: flex; justify-content: left;">
<tr><th style="white-space:nowrap;text-align:left;padding:5px 20px;">Library Crate</th><th style="text-align:left;padding:5px 20px;">Android</th><th style="text-align:left;padding:5px 20px;">iOS/macOS</th></tr>

<tr><td style="white-space:nowrap;vertical-align:top;">iota-client</td><td style="vertical-align:top;">iota-client = { version = "2.0.1-rc.7", default-features = false, features = [
    "stronghold"
] }</td>
<td style="vertical-align:top;">iota-client = { version = "2.0.1-rc.7", default-features = false, features = [
    "stronghold"
] }</td></tr>

<tr><td style="white-space:nowrap;vertical-align:top;">iota-wallet</td><td style="vertical-align:top;">iota-wallet = { git = "https://github.com/iotaledger/wallet.rs", rev = "05fcb303c657c6faf3cb772f3a3908647614d545", default-features = true}</td>
<td style="vertical-align:top;">iota-wallet = { git = "https://github.com/iotaledger/wallet.rs", branch = "develop", default-features = true}</td></tr>

<tr><td style="white-space:nowrap;vertical-align:top;"></td><td style="vertical-align:top;">(includes rocksdb v0.18.0)</td>
<td>(includes rocksdb v0.19.0)</td></tr>

<tr><td style="white-space:nowrap;vertical-align:top;">identity_iota</td><td style="vertical-align:top;">identity_iota = { version = "0.7.0-alpha.6", default-features = true }</td>
<td style="vertical-align:top;">identity_iota = { version = "0.7.0-alpha.6", default-features = true }</td></tr>

</table>

You need to include different dependencies for the crate iota-wallet. This is due to an unsolved issue with regard to the third-party library of rocksdb.
