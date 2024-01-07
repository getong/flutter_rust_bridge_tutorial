# Building for iOS

---

Completing the remaining steps for iOS. After successfully creating macOS, building the iOS version is relatively straightforward.

---

## iOS Setup

An common step for macOS / iOS is needed: creating an Xcode project **inside of the Rust library project folder** (_rust/_). This can be done using the `cargo-xcode` command.

1. To install the `cargo-xcode` command use:

   ```
   cargo install cargo-xcode@1.5.0
   ```

2. After the installation of the command, create the Rust Xcode project. **Make sure to be in the _rust/_ directory**. From the project's root folder you may switch into the right directory:

   ```
   cd rust
   ```

   ```
   cargo xcode
   ```

   ```
   cd ..
   ```

## Completing the iOS App

{{#include ../building-without-iota/flutter-and-rust/frb-example-app/ios-instructions.md:11:21}}

### Starting the iOS Simulator in VS Code

**Option 1**

<figure style="margin:0;"><img src="../assets/start-ios-simulator.png" alt="Start iOS Simulator"><figcaption style="font-size: 0.8em;text-align:center;"><p>Start iOS Simulator</p></figcaption></figure>

**Option 2**

As a shortcut, use the terminal and execute:

```
open -a simulator
```

### Video

Follow the video for the remaining steps.

<iframe 
    class="video"  
    src="https://www.youtube.com/embed/-dXodEDAL_Q" 
    title="Building a Simple App for SHIMMER - finalize the iOS part" 
    frameborder="0" 
    allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" 
    allowfullscreen>
</iframe>

### Troubleshooting

A user informed me about encountering issues when attempting to run the app on the iOS Simulator. Having not used the app for several months, I decided to test it myself and encountered some problems, too. Here is my response to the user.

---

Upon receiving your notification about the issue, I dedicated time today to rebuild the project. Since I initially set up and completed the project, there have been changes in my personal development environment. Specifically, I upgraded to macOS Sonoma 14.1.1 and installed Xcode 15. Upon launching VS Code, the project prompted me to execute "flutter pub upgrade" due to a change in the Flutter version during this period. Unfortunately, when attempting to run the project on iOS using "flutter run," the build process encountered a failure.

Despite not being aware of your specific circumstances, I managed to resolve all errors, successfully rebuild the project, and run it on the iOS Simulator, specifically using iPhone 15 Pro Max with iOS 17.0.

I followed these steps to address the issue:

1. Updated Rust to the latest version using "rustup update."
2. Deleted the Cargo.lock file.
3. Deleted the rust/target/ folder.
4. Deleted the following generated files: rust/src/bridge_generated.io.rs, rust/src/bridge_generated.rs, bridge_definitions.dart, bridge_generated.dart.
5. Removed the first generated line in rust/src/lib.rs.
6. Restarted VS Code.
7. Regenerated the Dart Code using the command in the tutorial, resulting in the creation of two Rust files and two Dart files.
8. Initially, VS Code displayed an error in the bridge_generated.rs file. After restarting VS Code, the error disappeared.
9. In the Terminal, navigated to the rust/ directory and executed "cargo build --target aarch64-apple-ios-sim," which was successful.
10. Returned to the root directory (cd ..) and ran "flutter run"
11. I received messages about "Xcode 15 compatibility" (or similar) and ultimately, the library and the app were successfully built, launching without any issues.

These steps were necessary because I initially created the project under macOS 13, Xcode 14, and lower versions of Flutter and Rust. I assume if you build the project from scratch using the Flutter-only version on GitHub and follow the steps outlined in the tutorial, you shouldn't encounter the same issue.
