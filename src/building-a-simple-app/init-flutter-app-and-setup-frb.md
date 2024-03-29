# Initialize the Flutter App and Setup the Flutter-Rust-Bridge

---

Watch the video! Step 1: A guide to connect Flutter and Rust for the Simple App Project.

---

<iframe 
    class="video"  
    src="https://www.youtube.com/embed/SItAUP65wJk" 
    title="Building a Simple App - Starting the project" 
    frameborder="0" 
    allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" 
    allowfullscreen>
</iframe>

## GitHub Repository

<a href="https://github.com/iota-for-flutter/simple_app" target="_blank">👉 &nbsp; GitHub Repo - Simple App (Flutter only)</a>

---

## Steps in the video

### In Terminal

```
git clone https://github.com/iota-for-flutter/simple_app.git
cd simple_app
code .
```

### In VSCode

```
cargo new --lib rust
cargo install flutter_rust_bridge_codegen

flutter pub add --dev ffigen:9.0.1 && flutter pub add ffi
flutter pub add flutter_rust_bridge
flutter pub add -d build_runner
flutter pub add -d freezed
flutter pub add freezed_annotation
```

### In Cargo.toml

```rust, ignore
[dependencies]
flutter_rust_bridge = "1"

[lib]
crate-type = ["staticlib", "cdylib"]
```

Add the Rust Code from the previous chapter.

{{#include ./core-api.md:427:}}
