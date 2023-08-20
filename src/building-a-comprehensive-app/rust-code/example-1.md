# Example 1

---

Rust adjustments for Example "Get Node Information".

---

## What adjustments do I need to make in Rust?

In summary, here are the steps you need to take to create the API function.

### Cargo.toml

Add these crates to the existing ones.

```toml
[dependencies]
iota-client = { version = "2.0.1-rc" }

tokio = { version = "1.28.2", features = ["full"] }
anyhow = { version = "1.0.71" }

# Serialization/Deserialization
serde = { version = "1.0.164", default-features = false, features = ["derive"] }
serde_json = { version = "1.0.97", default-features = false }
```

### api.rs - Used Paths

```rust,ignore
use iota_client::Client;
use tokio::runtime::Runtime;
use anyhow::Result;
```

### api.rs - Struct _NetworkInfo_

This struct bundles various network information. Through code generation of the Flutter-Rust-Bridge, the struct is translated into a Flutter class and becomes part of the `bridge_definitions.dart` file.

Therefore, it is not necessary to explicitly create it in Flutter! On the other hand, this means that the code generator needs to be executed once for the class to be seamlessly used in Flutter without any errors.

```rust,ignore
#[derive(Debug, Clone)]
pub struct NetworkInfo {
    pub node_url: String,
    pub faucet_url: String,
}
```

### api.rs - Function _get_node_info()_

```rust,ignore
#[allow(dead_code)]
pub fn get_node_info(network_info: NetworkInfo) -> Result<String> {

    let rt = Runtime::new().unwrap();
    rt.block_on(async {

        let node_url = network_info.node_url;

        // Create a client with that node.
        let client = Client::builder()
            .with_node(&node_url)?
            .with_ignore_node_health()
            .finish()?;

        // Get node info.
        let info = client.get_info().await?;

        Ok(serde_json::to_string_pretty(&info).unwrap())
        //Ok(info.node_info.base_token.name)
    })

}
```

## Checks using _cargo build_

---

> All checks should work without any issue. Please also refer to the corresponding video.

---

{{#include ./README.md:33:}}
