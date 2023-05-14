# Handling Code Changes

---

What to do in various situations when working with Flutter, Rust, and the Flutter-Rust Bridge.

---

### If the Rust API changes

If the Rust API changes, such as the addition of a new `pub fn` function or `pub struct`, or if parameters in an existing function change, you will need to call the `flutter_rust_bridge_codegen` function again.

> **Note**: Sometimes I've noticed that the state in VS Code doesn't get updated and still shows faulty files, after the code generation. In this case, it helps to close and reload the project window...

### If the business logic in Rust changes

If the business logic in Rust changes and the server is currently running, you'll need to stop and restart it. During the startup process, the library will be recompiled automatically with `cargo build` and linked to the app. You will observe in the console that the library named `rust` is being compiled.

### If there are changes in Flutter UI/business logic

If there are changes in Flutter UI/business logic, you may not have to do anything if the server is running (hot reload is triggered by saving), or you may need to reload the app if necessary. Exception: if you started the app by `flutter run`, you'd need to use the keyboard, e.g. "r" for hot reload and "R" for reload.
