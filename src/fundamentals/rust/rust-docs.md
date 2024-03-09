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

### Rust Docs for IOTA SDK

Download the library from Github:

<a href="https://github.com/iotaledger/iota-sdk" target="_blank">👉 &nbsp; IOTA SDK Library</a>

<figure style="margin:0;border: 1px solid green;">
<img src="../../assets/iota-sdk/iota-sdk-in-finder.png" alt=""><figcaption style="font-size: 0.8em;text-align:center;"><p style="margin: 4px 0 7px 0;">IOTA SDK in Finder</p></figcaption>
</figure>

Open VS Code and run the commands:

```
cd sdk
cargo doc --all-features --document-private-items --target-dir "rustdocs" --open
```

It will run several minutes and create the output folder "rustdocs":

<figure style="margin:0;border: 1px solid green;">
<img src="../../assets/iota-sdk/iota-sdk-creating-rust-docs.png" alt=""><figcaption style="font-size: 0.8em;text-align:center;"><p style="margin: 4px 0 7px 0;">Creating the Rust Docs for IOTA SDK</p></figcaption>
</figure>

Eventually, the browser will open automatically and present the root page of the documentation.

<figure style="margin:0;border: 1px solid green;">
<img src="../../assets/iota-sdk/rust-docs.png" alt=""><figcaption style="font-size: 0.8em;text-align:center;"><p style="margin: 4px 0 7px 0;">Home page of Rust docs for IOTA SDK</p></figcaption>
</figure>

> Tip: Delete the `target/` folder afterwards - it occupies 4.4 GB of space.

### Rust Docs for iota-client (deprecated)

Prior to executing the `cargo doc ...` command mentioned above, switch to the `client/` directory located at the root, rather than the `sdk/` directory.

> **Sometimes you are facing unresolved links**
>
> When executing the command, I encountered a handful of error messages due to unresolved links. In rustdoc, links are indicated by square brackets `[...]`. Fortunately, fixing these issues is a breeze: simply remove the square brackets i.e. the link, and the problematic areas will be swiftly resolved.
