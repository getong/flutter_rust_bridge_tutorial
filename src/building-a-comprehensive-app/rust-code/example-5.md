# Example 5

---

Rust adjustments for Example "Request Funds".

---

## What adjustments do I need to make in Rust?

In summary, here are the steps you need to take to create the API function.

### Cargo.toml

There is no need to add any crates. It's the same as in [Example 3](./example-3.md).

### api.rs - Used Paths

Add the function `iota_wallet::iota_client::request_funds_from_faucet`:

```rust,ignore
use iota_wallet::{
    iota_client::Client,
    iota_client::request_funds_from_faucet, // <- Add this one
    iota_client::constants::SHIMMER_COIN_TYPE,
    ClientOptions,
    account_manager::AccountManager,
    secret::{stronghold::StrongholdSecretManager as WalletStrongholdSecretManager},
    secret::{SecretManager as WalletSecretManager}
};
```

### api.rs - Function _request_funds()_

```rust,ignore
#[allow(dead_code)]
pub fn request_funds(network_info: NetworkInfo, wallet_info: WalletInfo) -> Result<String> {
    let rt = Runtime::new().unwrap();
    rt.block_on(async {
        let stronghold_filepath = wallet_info.stronghold_filepath;
        env::set_current_dir(&stronghold_filepath).ok();

        let faucet_url = network_info.faucet_url;

        // Use the function iota_wallet::iota_client::request_funds_from_faucet
        let faucet_response =
            request_funds_from_faucet(&faucet_url, &wallet_info.last_address).await?;

        Ok(faucet_response.to_string())
    })
}
```

## Checks using _cargo build_

{{#include ./example-4.md:51:}}
