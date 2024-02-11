# Libsodium library for iOS

---

Please refer to this chapter to learn about a **Workaround for iOS** that can be used **if you wish to utilize Stronghold**.

---

The _libsodium_ library is a widely used library for cryptography, offering a variety of functions for encryption, decryption, hashing, signatures, and more.

_libsodium-sys_ is a Rust wrapper around the _libsodium_ library. It provides the necessary Rust bindings and FFI (Foreign Function Interface) declarations to link and interact with the _libsodium_ library from Rust code.

**_libsodium-sys_ is a dependency of the _stronghold-runtime_ crate.**

> It's crucial to grasp that the wrapper (libsodium-sys) inherently includes a snapshot of libsodium and automatically builds it by default, which doesn't work for iOS as we have seen. **HOWEVER, you have the option to substitute this bundled version with any precompiled library that you've built independently.** This is the task at hand. The environment variables SODIUM_LIB_DIR and SODIUM_SHARED play a pivotal role in this context.

### GitHub Repositories

<a href="https://github.com/jedisct1/libsodium" target="_blank">👉 &nbsp; GitHub Repo - libsodium</a>

<a href="https://github.com/sodiumoxide/sodiumoxide#extended-usage" target="_blank">👉 &nbsp; GitHub Repo - libsodium-sys - Extended usage</a>

## Workaround

These are the three steps:

In Step 1, create the `libsodium.a` library file for the iOS target.

In Step 2, test the built library by using the environment variables SODIUM_LIB_DIR and SODIUM_SHARED.

In Step 3, include the created `libsodium.a` in the Runner's build process in Xcode as static library and define SODIUM_LIB_DIR and SODIUM_SHARED in Rust's build phase in Xcode.

### Step 1: Create libsodium.a

You'll need to clone the libsodium repository in a new IDE project.

```
git clone https://github.com/jedisct1/libsodium.git
```

You will need to install some tools on your macOS:

a) [Homebrew](https://brew.sh)

Check with `brew --version` if you can skip the installation. If already installed, a version is returned. Otherwise install _Homebrew_ with the command:

```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

b) autoconf

Check with `autoconf --version` if you can skip the installation. If already installed, a version is returned. Otherwise install _autoconf_ with the command:

```
brew install autoconf
```

c) automake

Check with `automake --version` if you can skip the installation. If already installed, a version is returned. Otherwise install _automake_ with the command:

```
brew install automake
```

d) libtool

Install _libtool_ with the command:

```
brew install libtool
```

If it's already installed, you'll get a message returned: "Warning: libtool 2.4.7 is already installed and up-to-date."

---

Once you've setup everything, you can navigate into the "libsodium" directory and execute:

```
./autogen.sh -s
```

This will create the `configure` file. The next command will create the libraries for several Apple targets (iOS, macOS, tvOS, watchOS):

```
./dist-build/apple-xcframework.sh
```

> To save time feel free to adjust the script in order to skip some of the targets.

The console output tells you where you will find the wanted `libsodium.a`: It's located in the folder "\<path/to/your/libsodium\>/libsodium-apple/tmp/ios64/lib/".

### Step 2: Test the built library

Navigate to the `rust` folder of the Playground App you have created in the Android section.

Now, I recommend to test the cargo build in Terminal by setting the following environment variables before executing the commands. Please replace "_/path/to/library_" with the correct directory path to your library (do NOT append "_libsodium.a_" at the end of the path).

```
SODIUM_LIB_DIR="/path/to/library" SODIUM_SHARED=1 cargo build --target aarch64-apple-ios
```

e.g.

```
SODIUM_LIB_DIR="/Users/yourname/libsodium/libsodium-apple/tmp/ios64/lib" SODIUM_SHARED=1 cargo build --target aarch64-apple-ios
```

### Step 3: Integrate libsodium.a in Xcode and in the build process

In the aforementioned test, we defined the variables SODIUM_LIB_DIR and SODIUM_SHARED within the command line. However, these definitions will not persist.

To ensure proper setup, you need to

- Open the Runner.xcworkspace
- Add a new group "sodium" in the Runner
- Switch to Finder, copy the libsodium.a from `/Users/yourname/libsodium/libsodium-apple/tmp/ios64/lib` into the new folder `/Users/yourname/playground_app/ios/Runner/sodium`
- Simultaneously open the Runner.xcworkspace and drag the libsodium.a library from the Finder into the newly created group "sodium"
- Adjust the _Build Rules_ (line 25) of the target "Rust" in order to persist the environment variables SODIUM_LIB_DIR and SODIUM_SHARED:

  ```
  SODIUM_LIB_DIR="/Users/yourname/playground_app/ios/Runner/sodium"
  SODIUM_SHARED=1
  ```

## Give it a try

Continue and go back to chapter [-> Building for iOS](../building-for-ios/).
