# Example 6

---

Rust adjustments for Example "Check Balance". 2023-08-20: INTERMEDIATE VERSION - NEEDS TO BE REVIEWED.

---

## What adjustments do I need to make in Rust?

In summary, here are the steps you need to take to create the API function.

### Cargo.toml

There is no need to add any crates. It's the same as in [Example 5](./example-5.md).

### api.rs - Used Paths

There is no need to add any path. It's the same as in [Example 5](./example-5.md).

### api.rs - Struct _BaseCoinBalance_

This struct bundles some information about total and available amounts. Through code generation of the Flutter-Rust-Bridge, the struct is translated into a Flutter class and becomes part of the `bridge_definitions.dart` file.

Therefore, it is not necessary to explicitly create it in Flutter! On the other hand, this means that the code generator needs to be executed once for the class to be seamlessly used in Flutter without any errors.

```rust,ignore
#[derive(Debug, Clone)]
pub struct BaseCoinBalance {
    /// Total amount
    pub total: u64,
    /// Balance that can currently be spent
    pub available: u64,
}
```

### api.rs - Function _check_balance()_

```rust,ignore
#[allow(dead_code)]
pub fn check_balance(wallet_info: WalletInfo) -> Result<BaseCoinBalance> {
    let rt = Runtime::new().unwrap();
    rt.block_on(async {
        let stronghold_filepath = wallet_info.stronghold_filepath;
        env::set_current_dir(&stronghold_filepath).ok();

        // Create the account manager
        let manager = AccountManager::builder().finish().await?;

        // Get the account we generated with `create_wallet_account`
        let account = manager.get_account((&wallet_info.alias).to_string()).await?;

        // Sync and get the balance
        let _account_balance = account.sync(None).await?;
        // If already synced, just get the balance
        let account_balance = account.balance().await?;

        //let base_coin_balance = account_balance.base_coin;
        let base_coin_balance = BaseCoinBalance {
            total: account_balance.base_coin.total,
            available: account_balance.base_coin.available,
        };
        //let total = account_balance.base_coin.total;
        //println!("{:?}", account_balance);

        //Ok(total.to_string())
        //Ok(base_coin_balance)
        Ok(base_coin_balance)
    })
}
```
