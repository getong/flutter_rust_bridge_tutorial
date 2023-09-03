# Building for Android

---

2023-08-20: INTERMEDIATE VERSION - NEEDS TO BE REVIEWED.

---

## Android Setup

If you haven't already, install the `cargo-ndk` command using:

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
        environment SODIUM_LIB_DIR: "/Users/yourname/playground_app/android/app/src/main/jniLibs/arm64-v8a" // <-- ATTENTION: CHECK THE CORRECT FOLDER!!!
        environment SODIUM_SHARED: 1
        commandLine 'cargo', 'ndk',
                // the 2 ABIs below are used by real Android devices
                // '-t', 'armeabi-v7a',
                '-t', 'arm64-v8a',
                // the below 2 ABIs are usually used for Android simulators,
                // add or remove these ABIs as needed.
                // '-t', 'x86',
                // '-t', 'x86_64',
                '-o', '../android/app/src/main/jniLibs', 'build'
        if (profileMode != null) {
            args profileMode
        }
    }
}
```

> Please note that due to the use of libsodium, there are two additional lines related to SODIUM_LIB_DIR and SODIUM_SHARED (compared to the former configurations) -> please also refer to [Libsodium library for Android](./rust-code/example-3/libsodium.md).

## Enabling Dynamic Library Loading

Even if the our dynamic library `librust.so` has been successfully compiled in Android, you haven't won yet. It also needs to be able to be loaded.

There is an error message that only becomes apparent when using the app, after you have completed all steps and successfully launched the app in Flutter.

Let's say you want to invoke the "Generate Mnemonic" example. Here's where you stumble when you click on "Execute" in Example 2, as the console displays the following message:

<figure style="margin:0;"><img src="../assets/playground/android-error-loading-dynamic-lib-failed.png" alt="Error message: Failed to load dynamic library 'librust.so'"><figcaption style="font-size: 0.8em;text-align:center;"><p>Error message: "Failed to load dynamic library 'librust.so'"</p></figcaption></figure>

### Workaround

As the message text also says: The "library 'libc++\_shared.so' is not found". So, adding this library is the solution.

**Where do we get this one from?**

It is provided by the NDK. For example, if you're using NDK v25.2.9519653 on macOS, navigate to the folder _~/Library/Android/sdk/ndk/25.2.9519653/toolchains/llvm/prebuilt/darwin-x86_64/sysroot/usr/lib/_ where you will find subfolders for each of the targets:

<figure style="margin:0;"><img src="../assets/playground/libcplusplus_copy.png" alt="Location to copy libc++_shared.so from"><figcaption style="font-size: 0.8em;text-align:center;"><p>Location to copy libc++_shared.so from</p></figcaption></figure>

> Alternatively, here are also some download links from Android NDK 25 on macOS:
>
> [arm64-v8a/libc++\_shared.so](../assets/download/arm64-v8a/libc++_shared.so)
>
> [armeabi-v7a/libc++\_shared.so](../assets/download/armeabi-v7a/libc++_shared.so)
>
> [x86/libc++\_shared.so](../assets/download/x86/libc++_shared.so)
>
> [x86_64/libc++\_shared.so](../assets/download/x86_64/libc++_shared.so)

**Where do we need to place it?**

Once you have copied or downloaded the `libc++_shared.so` library, you should ensure that you place it in a directory where it can be accessed by your application. To simplify the process, you can place it in the same folder as the `librust.so` library.

<figure style="margin:0;"><img src="../assets/playground/libcplusplus_paste.png" alt="Location to paste libc++_shared.so"><figcaption style="font-size: 0.8em;text-align:center;"><p>Location to paste libc++_shared.so</p></figcaption></figure>
