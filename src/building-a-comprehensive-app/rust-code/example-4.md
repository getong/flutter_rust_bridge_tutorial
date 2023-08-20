# Example 4

---

Rust adjustments for Example "Generate Address". 2023-08-20: INTERMEDIATE VERSION - NEEDS TO BE REVIEWED.

---

## What adjustments do I need to make in Rust?

In summary, here are the steps you need to take to create the API function.

### Cargo.toml

There is no need to add any crates. It's the same as in [Example 3](./example-3.md).

### api.rs - Used Paths

There is no need to add any path. It's the same as in [Example 3](./example-3.md).

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
