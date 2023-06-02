# Dependencies and Features

---

Cargo.toml and your friend: Cargo.lock

---

## Motivation

Imagine that you have created your Rust API in the directory `rust/` within your Flutter project, added the necessary dependencies, and your code appears to be error-free.

With a hopeful spirit, you press the play button. The code starts compiling. It compiles and compiles, and then it happens: a red error message halts your enthusiasm.

It's possible that you have included a dependency whose version is not compatible with the target platform - such as the iota-wallet. What works on iOS may not necessarily function on Android, and vice versa. While we aim to cross-compile Rust code, there are a few cases where it doesn't go smoothly.

To find a solution to the problem, you need to first determine:

- Which library is causing the error?
- Which version of the library is being used?
- What is the dependency hierarchy that has been established?

By understanding these aspects, you can begin troubleshooting and seeking a resolution for the issue at hand.

## Dependencies

_Dependencies_ are managed using the `Cargo.toml` file. This file serves as a manifest for the project, where you declare the dependencies required for your code to compile and run. Dependencies are specified under the _[dependencies]_ section, where you can list the name and version of each dependency. Cargo uses this information to fetch and manage the dependencies automatically.

Several options provide flexibility in managing dependencies. Version-based specifications ensure compatibility with specific versions, while Git-based specifications allow fetching libraries directly from Git repositories, enabling experimentation with different branches, revisions, or even forks of a library.

### Version-based specifications

Version-based specifications are downloaded from the Crate Repository at [https://crates.io](https://crates.io).

```toml
[dependencies]
library-name = "1.2.0"
```

In this example, the project depends on version 1.2.0 of the "library-name" crate.

Developers can also use version constraints:

```toml
[dependencies]
library-name = ">= 1.0, < 2.0"
```

Here, the project allows any version from 1.0 (inclusive) up to, but not including, version 2.0 of the "library-name" crate.

### Git-based specifications

```toml
[dependencies]
library-name = { git = "https://github.com/username/library.git", branch = "develop" }
```

In this example, the project fetches the "library-name" crate from the specified Git repository, using the "develop" branch.

> **Warning**
>
> When employing this form of dependency specification, the latest commit from the specified branch will be fetched each time changes are made to Cargo.toml. It is essential to be aware that by relying on this method, there is a possibility that library developers may introduce unintended modifications to the library.

Developers can also specify a specific commit hash (revision):

```toml
[dependencies]
library-name = { git = "https://github.com/username/library.git", rev = "abcdef123456" }
```

Here, the project fetches the "library-name" crate at the specified commit with the hash "abcdef123456".

> **Exercise**
>
> Find the hash value of the last commit BEFORE rocksdb 0.19 was brought back into wallet.rs.
>
> 1. Start on the page [https://github.com/iotaledger/wallet.rs/commits/develop](https://github.com/iotaledger/wallet.rs/commits/develop).
> 2. Go back in history by clicking the "Older" button at the bottom of the page.
> 3. Continue navigating through the commits until you find the commit titled "Bring back rocksdb 0.19".
> 4. Copy the full SHA (hash) of the commit that is immediately before the "Bring back rocksdb 0.19" commit.
>
> The corresponding hash value should be: 05fcb303c657c6faf3cb772f3a3908647614d545. You could use this hash as the value for `rev = "..."` in the dependency definition for iota-wallet, where rocksdb version **0.18** is included.
>
> Tipp: It's somewhere in December 2022.

### Reading the Cargo.lock file

The Cargo.lock file is an automatically generated file in Rust projects that serves as a lock file, ensuring deterministic builds. It records the exact versions of all dependencies used in the project, including transitive dependencies.

This file helps in guaranteeing that subsequent builds of the project will use the same dependency versions, providing consistency and reproducibility.

The Cargo.lock file is automatically updated by Cargo when dependencies are added, removed, or updated, and it should be committed to version control to ensure consistent builds across different environments.

## Features

_Features_ are a way to enable or disable optional functionalities in the dependencies of a library or package. They allow developers to reduce the size and complexity of dependencies by selecting only the features they actually need.

You can imagine that by configuring features in `Cargo.toml`, you can switch on or switch off specific functionalities. By disabling certain features, you also ensure that the associated dependent libraries are not compiled into your source code. So, if you encounter issues with specific third-party libraries, you can narrow down the problems by disabling certain features for testing purposes.

Features can be specified in the `Cargo.toml` file of a Rust project. You can find an explanation of the configuration with examples in this article:

<a href="https://dev.to/rimutaka/cargo-features-explained-with-examples-194g" target="_blank">👉 &nbsp; Cargo [features] explained with examples</a>
