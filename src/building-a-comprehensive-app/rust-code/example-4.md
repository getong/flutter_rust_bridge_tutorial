# Example 4

---

Rust adjustments for Example "Generate Address".

---

## What adjustments do I need to make in Rust?

In summary, here are the steps you need to take to create the API function.

### Cargo.toml

There is no need to add any crates. It's the same as in [Example 3](./example-3/).

### api.rs - Used Paths

There is no need to add any path. It's the same as in [Example 3](./example-3/).

### api.rs - Function _generate_address()_

```rust,ignore
#[allow(dead_code)]
pub fn generate_address(wallet_info: WalletInfo) -> Result<String> {
    let rt = Runtime::new().unwrap();
    rt.block_on(async {
        let stronghold_filepath = wallet_info.stronghold_filepath;
        env::set_current_dir(&stronghold_filepath).ok();

        // Create the account manager
        let manager = AccountManager::builder().finish().await?;

        // Get the account we generated with `01_create_wallet`
        let account = manager.get_account((&wallet_info.alias).to_string()).await?;

        // Set the stronghold password
        manager
            .set_stronghold_password(&wallet_info.stronghold_password)
            .await?;

        let addresses = account.generate_addresses(1, None).await?;
        //println!("Generated address: {}", address[0].address().to_bech32());

        Ok(addresses[0].address().to_bech32())
    })
}
```

## Checks using _cargo build_

---

> All checks [(-> except iOS Simulator)](./example-3/#ios-simulator) should work without any issue. Please also refer to the explanations of [Example 3](./example-3/) and the corresponding video (2023-09-03: ToDo).

---

To examine the various targets, you should navigate from _playground_app_ root directory to the _rust_ directory:

```
cd rust
```

Then, within the _rust_ directory, excute the following commands.

### Android

If you haven't already, install the `cargo-ndk` command using:

```
cargo install cargo-ndk
```

I only check the ABI _arm64-v8a_.

a) If you've had NO problems with the 3rd party library _libsodium_, use the command:

```
cargo ndk -t arm64-v8a build
```

b) If you've HAD problems with the 3rd party library _libsodium_, use the command:

```
SODIUM_LIB_DIR="/path/to/libsodium" SODIUM_SHARED=1 cargo ndk -t arm64-v8a build
```

e.g.

```
SODIUM_LIB_DIR="/Users/yourname/playground_app/android/app/src/main/jniLibs/arm64-v8a" SODIUM_SHARED=1 cargo ndk -t arm64-v8a build
```

[-> Why do you need SODIUM_LIB_DIR and SODIUM_SHARED here?](./example-3/libsodium.md)

### macOS

```
cargo build --target aarch64-apple-darwin
```

### iOS Simulator

This check will fail, please refer to the explanations of [Example 3](./example-3/#ios-simulator) (Libsodium).

```
cargo build --target aarch64-apple-ios-sim
```

### iOS Device

```
cargo build --target aarch64-apple-ios
```
