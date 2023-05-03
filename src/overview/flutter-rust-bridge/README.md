# 🌁 Flutter Rust Bridge

---

Unifying Flutter and Rust: Harnessing the power of two technologies.

---

On one hand, we have Flutter, a powerful framework for building user interfaces and applications. On the other hand, we have Rust, a high-performance programming language known for its safety and efficiency. But how can we combine these two technologies?

Enter the Flutter Rust Bridge, a GitHub project designed to address this very challenge of integrating both technologies.

<a href="https://cjycode.com/flutter_rust_bridge" target="_blank">👉 &nbsp; Flutter Rust Bridge - User Guide</a>

<a href="https://github.com/fzyzcjy/flutter_rust_bridge" target="_blank">👉 &nbsp; Flutter Rust Bridge - Github Page</a>

> It is important to highlight that, currently, the missing component in this equation is IOTA. However, this tutorial will delve into its integration in the later sections.

## Code Generator

The Code Generator is a Rust executable that processes Rust code and produces two outputs: a generated module `bridge_generated.rs` on the Rust side and a Dart file `bridge_generated.dart` with definitions and implementations on the Dart side. The two files act as a bridge between the two programming languages.

> The Code Generator offers a range of options that you can access by using this terminal command:
>
> `flutter_rust_bridge_codegen --help`

This command provides you with a list of available options and their respective descriptions, allowing you to customize the code generation process according to your specific needs.

In the [Workflow](../how-everything-works-together/workflow.html) section documenting an **Android** demo, I used these options:

```
flutter_rust_bridge_codegen \
--rust-input rust/src/api.rs \                      // Source file
--dart-output ./lib/bridge_generated.dart \         // Output in Dart folder
--dart-decl-output ./lib/bridge_definitions.dart    // Seperate definition file in Dart folder
```

When working on code for **iOS**, you will require the following options for the Code Generator:

```
flutter_rust_bridge_codegen \
--rust-input rust/src/api.rs \                      // Source file
--dart-output ./lib/bridge_generated.dart \         // Output in Dart folder
--dart-decl-output ./lib/bridge_definitions.dart \  // Seperate definition file in Dart folder
--c-output ios/Runner/bridge_generated.h            // Generate a C header in the correct iOS folder
```

Similar for **macOS**:

```
flutter_rust_bridge_codegen \
--rust-input rust/src/api.rs \                      // Source file
--dart-output ./lib/bridge_generated.dart \         // Output in Dart folder
--dart-decl-output ./lib/bridge_definitions.dart \  // Seperate definition file in Dart folder
--c-output macos/Runner/bridge_generated.h          // Generate a C header in the correct macOS folder
```

---

> **A task for you**
>
> For other target platforms such as Browser, Linux, and Windows, please refer to the User Guide for specific instructions on using the Code Generator. Additionally, you have the option to create a YAML config file and utilize the command `flutter_run_bridge_codegen [CONFIG_FILE]` to streamline the code generation process. I encourage you to give it a try and explore the flexibility and convenience it offers.
