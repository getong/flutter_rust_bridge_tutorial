# Part 3: Common macOS/iOS specific instructions

---

FRB Template App: Applying the **<a href="../frb-example-app">modified Workflow</a>**.

---

## Common macOS and iOS steps

An common step for macOS / iOS is needed: creating an Xcode project **inside of the Rust library project folder** (_rust/_). This can be done using the `cargo-xcode` command.

This Cargo subcommand is used to generate all Xcode project files for Rust projects. It will also create a _build rule_ that will be used to create a dynamic and a static library from the Rust library code in Xcode's build step. If you don't remember, take a look back and read the section <a href="../../fundamentals/xcode/essentials.md">Xcode Essentials</a>.

To install the `cargo-xcode` command use:

```
cargo install cargo-xcode
```

After the installation of the command, create the Rust Xcode project. Make sure to be in the _rust/_ directory. From the project's root folder you may switch into the right directory:

```
cd rust
```

```
cargo xcode
```

```
cd ..
```

<figure style="margin:0;">
<a href="../../../assets/create_rust_xcodeproj_before.jpg" target="_blank">
<img src="../../../assets/create_rust_xcodeproj_before.jpg" alt=""><figcaption style="font-size: 0.8em;text-align:center;"><p>Before building the Rust Xcode Project (click to enlarge)</p></figcaption>
</a>
</figure>

> In this picture puzzle, you need to find the differences between two images. First, carefully examine the "before" image above ⬆️ - take note of all the details - and then look at the "after" image below ⬇️ and try to identify the differences.

<figure style="margin:0;">
<a style="width:50%" href="../../../assets/create_rust_xcodeproj_after.jpg" target="_blank">
<img src="../../../assets/create_rust_xcodeproj_after.jpg" alt=""><figcaption style="font-size: 0.8em;text-align:center;"><p>After building the Rust Xcode Project (click to enlarge)</p></figcaption>
</a>
</figure>

###
