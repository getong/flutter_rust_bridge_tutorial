# Part 2: Android specific instructions

---

FRB Template App: Applying the **<a href="../frb-example-app">modified Workflow</a>**.

---

## Android steps

### Generate the Dart Interface

Use this command (you need to be in the root of your project):

```
flutter_rust_bridge_codegen \
--rust-input rust/src/api.rs \
--dart-output ./lib/bridge_generated.dart \
--dart-decl-output ./lib/bridge_definitions.dart \
```

### Set up the Flutter Rust Bridge (2)

To install the `cargo-ndk` command use:

```
cargo install cargo-ndk
```

In _android/app/build.gradle_, fix error:

```
Replace GradleException by FileNotFoundException
```

In _android/app/build.gradle_, add at the bottom:

```
[
        Debug: null,
        Profile: '--release',
        Release: '--release'
].each {
    def taskPostfix = it.key
    def profileMode = it.value
    tasks.whenTaskAdded { task ->
        if (task.name == "javaPreCompile$taskPostfix") {
            task.dependsOn "cargoBuild$taskPostfix"
        }
    }
    tasks.register("cargoBuild$taskPostfix", Exec) {
        workingDir "../../rust"  // <-- ATTENTION: CHECK THE CORRECT FOLDER!!!
        environment ANDROID_NDK_HOME: "$ANDROID_NDK"
        commandLine 'cargo', 'ndk',
                '-t', 'arm64-v8a',
               '-o', '../android/app/src/main/jniLibs', 'build'
        if (profileMode != null) {
            args profileMode
        }
    }
}
```

I only included the ABI _arm64-v8a_ from my Android Emulator.
