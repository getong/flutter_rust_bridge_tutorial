# Example 2

---

Rust adjustments for Example "Generate Mnemonic".

---

## What adjustments do I need to make in Rust?

In summary, here are the steps you need to take to create the API function.

### Cargo.toml

There is no need to add any crates. It's the same as in [Example 1](./example-1.md).

### api.rs - Used Paths

There is no need to add any path. It's the same as in [Example 1](./example-1.md).

### api.rs - Function _get_node_info()_

```rust,ignore
#[allow(dead_code)]
pub fn generate_mnemonic() -> String {
    let mnemonic = Client::generate_mnemonic();
    mnemonic.unwrap()
}
```

## Checks using _cargo build_

---

> All checks should work without any issue. Please also refer to the corresponding video.

---

{{#include ./README.md:33:}}
