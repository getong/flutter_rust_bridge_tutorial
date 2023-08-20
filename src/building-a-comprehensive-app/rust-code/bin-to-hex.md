# Bin to Hex

---

Rust adjustments for the left side bar tool "Bin to Hex".

---

## What adjustments do I need to make in Rust?

In summary, here are the steps you need to take to create the API function.

### Cargo.toml

There is no need to add any crates.

### api.rs - Used Paths

Add this path:

```rust,ignore
use std::u32;
```

### api.rs - Function _bin_to_hex()_

```rust,ignore
pub fn bin_to_hex(val: String, len: usize) -> String {
    let n: u32 = u32::from_str_radix(&val, 2).unwrap();
    format!("{:01$x}", n, len * 2)
}
```
