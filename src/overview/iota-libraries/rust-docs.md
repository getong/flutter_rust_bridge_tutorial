# Create Rust Docs

---

Documentation in Rust: Can be helpful, but ... crate docs can also be quite incomplete.

---

Another way to familiarize yourself with a library is to look at the documentation. With Rust, we can instantly create documentation based on the comments in the source code.

<a href="https://doc.rust-lang.org/stable/rustdoc/" target="_blank">👉 &nbsp; The rustdoc book</a>

There is the `rustdoc` command, but alternatively you can use `cargo doc` which uses rustdoc behind the scenes.

<a href="https://doc.rust-lang.org/cargo/commands/cargo-doc.html" target="_blank">👉 &nbsp; The cargo book - cargo doc</a>

> **Hints**
>
> By default, the rustdoc engine evaluates only the enabled features. To create the documentation that covers the whole code, use the option `--all-features`.
>
> To include non-public items, use the `--document-private-items` flag.
>
> All options are displayed by executing `cargo doc --help`.

So my favorite command is:

```
cargo doc --all-features --document-private-items --target-dir "rustdocs" --open
```

When you run the command, rustdoc will scan all files and create a working folder `debug/` and an output folder `doc/` inside the target directory. Depending on the number of scanned crates this might take a while.

### iota-client

Before running the above `cargo doc ...` command change to the `client/` directory.

> **Sometimes you are facing unresolved links**
>
> When executing the command, I encountered a handful of error messages due to unresolved links. In rustdoc, links are indicated by square brackets `[...]`. Fortunately, fixing these issues is a breeze: simply remove the square brackets i.e. the link, and the problematic areas will be swiftly resolved.
