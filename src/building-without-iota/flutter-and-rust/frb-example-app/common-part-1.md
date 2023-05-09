# Part 1: Initialization and Rust part

---

FRB Template App: Applying the **<a href="../frb-example-app">modified Workflow</a>**.

---

## Create the Flutter App

In the Terminal App, create an empty Flutter project - I'll name it _example2_ - and open it in Visual Code:

```
flutter create --empty example2
```

```
cd example2
```

```
code .
```

## Create the Rust Library Project

In VS Code open the terminal and execute:

```
cargo new --lib rust
```

## Setup the Flutter Rust Bridge (1)

```
cargo install flutter_rust_bridge_codegen

flutter pub add --dev ffigen && flutter pub add ffi

flutter pub add flutter_rust_bridge

flutter pub add -d build_runner

flutter pub add -d freezed

flutter pub add freezed_annotation
```

In _Cargo.toml_ add:

```
[dependencies]
flutter_rust_bridge = "1"

[lib]
crate-type = ["staticlib", "cdylib"]
```

## Development of the Rust API of the FRB Template App

### App specific dependencies

The `Cargo.toml` should look like (add _anyhow_):

```
[dependencies]
anyhow = "1"
flutter_rust_bridge = "1"
```

### Rust code

Create the file `rust/src/api.rs` and add this content:

```
// This is the entry point of your Rust library.
// When adding new code to your project, note that only items used
// here will be transformed to their Dart equivalents.

// A plain enum without any fields. This is similar to Dart- or C-style enums.
// flutter_rust_bridge is capable of generating code for enums with fields
// (@freezed classes in Dart and tagged unions in C).
pub enum Platform {
    Unknown,
    Android,
    Ios,
    Windows,
    Unix,
    MacIntel,
    MacApple,
    Wasm,
}

// A function definition in Rust. Similar to Dart, the return type must always be named
// and is never inferred.
pub fn platform() -> Platform {
    // This is a macro, a special expression that expands into code. In Rust, all macros
    // end with an exclamation mark and can be invoked with all kinds of brackets (parentheses,
    // brackets and curly braces). However, certain conventions exist, for example the
    // vector macro is almost always invoked as vec![..].
    //
    // The cfg!() macro returns a boolean value based on the current compiler configuration.
    // When attached to expressions (#[cfg(..)] form), they show or hide the expression at compile time.
    // Here, however, they evaluate to runtime values, which may or may not be optimized out
    // by the compiler. A variety of configurations are demonstrated here which cover most of
    // the modern oeprating systems. Try running the Flutter application on different machines
    // and see if it matches your expected OS.
    //
    // Furthermore, in Rust, the last expression in a function is the return value and does
    // not have the trailing semicolon. This entire if-else chain forms a single expression.
    if cfg!(windows) {
        Platform::Windows
    } else if cfg!(target_os = "android") {
        Platform::Android
    } else if cfg!(target_os = "ios") {
        Platform::Ios
    } else if cfg!(all(target_os = "macos", target_arch = "aarch64")) {
        Platform::MacApple
    } else if cfg!(target_os = "macos") {
        Platform::MacIntel
    } else if cfg!(target_family = "wasm") {
        Platform::Wasm
    } else if cfg!(unix) {
        Platform::Unix
    } else {
        Platform::Unknown
    }
}

// The convention for Rust identifiers is the snake_case,
// and they are automatically converted to camelCase on the Dart side.
pub fn rust_release_mode() -> bool {
    cfg!(not(debug_assertions))
}
```

Insert this line in `lib.rs`:

```
mod api;
```
