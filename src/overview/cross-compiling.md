# 🏭 Cross-Compiling

---

Exploring the required Targets.

---

The Rust compiler functions as a cross-compiler by default. It allows us to translate our Rust code into the target platform and package it as a library. However, to enable this functionality, we need to ensure that all the required targets are installed on the host computer. In this chapter, we will delve into the relevant targets for _IOTA for Flutter_.

<a href="https://doc.rust-lang.org/rustc/targets/index.html" target="_blank">👉 &nbsp; Rust Targets</a>

## About Targets

In the context of software development, a "target" typically refers to the platform or environment for which software is being developed or compiled. The Components of a target specification can vary depending on the context and tooling being used.

1. **Architecture**: The target architecture specifies the instruction set and hardware architecture for which the software is being compiled or built. Examples include x86, x86_64, ARMv7, ARMv8, etc.

2. **Vendor**: The vendor component indicates the company or organization associated with the target platform. It helps identify the specific platform or ecosystem for which the software is intended. Examples include apple, android, linux, windows, etc.

3. **Operating System**: The operating system component represents the software layer that manages system resources and provides services to applications. It defines the environment in which the software will run. Examples include darwin (macOS, iOS), linux, windows, android, etc.

4. **ABI (Application Binary Interface)**: The ABI component defines the interface between the compiled application code and the operating system and hardware. It defines how functions are called, how parameters and return values are passed, how memory is allocated, and other low-level details of interaction between software and the underlying system.

### &nbsp;

### Targets needed for macOS and iOS Development

The Target Structure typically consists of the following Components:

    <Architecture>-<Vendor>-<Operating System>

<table style="display: flex; justify-content: left;">
<tr><th style="text-align:left;padding:5px 20px;">Target</th><th style="text-align:left;padding:5px 20px;">Meaning</th></tr>
<tr><td style="white-space:nowrap;vertical-align:top;">aarch64-apple-darwin</td>
<td>Targeting Apple devices running macOS with ARM 64-bit architecture (Apple Silicon).</td></tr>
<tr><td style="white-space:nowrap;vertical-align:top;">x86_64-apple-darwin</td>
<td>Targeting Apple devices running macOS with Intel 64-bit x86 architecture .</td></tr>
<tr><td style="white-space:nowrap;vertical-align:top;"></td>
<td></td></tr>
<tr><td style="white-space:nowrap;vertical-align:top;">aarch64-apple-ios</td>
<td>Targeting iOS devices with ARM 64-bit architecture, as used since the iPhone 5S and later, the iPad Air, Air 2 and Pro, with the A7 and later chips.</td></tr>
<tr><td style="white-space:nowrap;vertical-align:top;">armv7s-apple-ios</td>
<td>Targeting iOS devices with 32-bit ARMv7s architecture ("old"), used in A6 and A6X chips on iPhone 5, iPhone 5C and iPad 4.</td></tr>
<tr><td style="white-space:nowrap;vertical-align:top;">armv7-apple-ios</td>
<td>Targeting iOS devices with 32-bit ARMv7 architecture, used in A5 chip ("old").</td></tr>
<tr><td style="white-space:nowrap;vertical-align:top;"></td>
<td></td></tr>
<tr><td style="white-space:nowrap;vertical-align:top;">aarch64-apple-ios-sim</td>
<td>Targeting iOS Simulator for Xcode 12 and later on hosts with ARM 64-bit architecture.</td></tr>
<tr><td style="white-space:nowrap;vertical-align:top;">x86_64-apple-ios-sim</td>
<td>Targeting iOS Simulator for Xcode 12 and later on hosts with 64-bit x86 architecture.</td></tr>
<tr><td style="white-space:nowrap;vertical-align:top;">i386-apple-ios</td>
<td>Targeting iOS Simulator on hosts with 32-bit x86 architecture ("old").</td></tr>
</table>

### &nbsp;

### Targets needed for Android Development

In the context of _IOTA for Flutter_ the Android Native Development Kit (NDK) must be installed. It is used to provide the necessary tools and APIs to interface with the cross-compiled library. The Target Structure then follows the format:

    <Architecture>-<Operating_system>-<ABI*>

ABI\* : As part of the Target, the ABI component is different to the correct ABI naming, due to historical reasons or to maintain compatibility. The correct ABI naming is listed in the second column.

<table style="display: flex; justify-content: left;">
<tr><th style="text-align:left;padding:5px 20px;">Target</th><th style="text-align:left;padding:5px 20px;">ABI</th><th style="text-align:left;padding:5px 20px;">Meaning</th></tr>
<tr><td style="white-space:nowrap;vertical-align:top;">aarch64-linux-android</td><td style="white-space:nowrap;vertical-align:top;">arm64-v8a</td>
<td>Targeting Android devices on ARM 64-bit architecture (most modern ARM-based Android devices).</td></tr>
<tr><td style="white-space:nowrap;vertical-align:top;">armv7-linux-androideabi</td><td style="white-space:nowrap;vertical-align:top;">armeabi-v7a</td>
<td>Targeting Android devices on ARMv7 architecture (older ARM-based Android devices).</td></tr>
<tr><td style="white-space:nowrap;vertical-align:top;">x86_64-linux-android</td><td style="white-space:nowrap;vertical-align:top;">x86_64</td>
<td>Targeting Android devices on 64-bit x86 architecture (Android emulators, modern x86-based devices).</td></tr>
<tr><td style="white-space:nowrap;vertical-align:top;">i686-linux-android</td style="white-space:nowrap;vertical-align:top;"><td>x86</td>
<td>Targeting Android devices on 32-bit x86 architecture (Android emulators, older x86-based devices).</td></tr>
</table>

### &nbsp;

## Which targets are installed on my system?

To find out which targets are installed on your system, run the following command:

`rustc --print target-list`

### &nbsp;

## How to add missing targets

When you first install a toolchain, rustup installs only the standard library for your host platform - that is, the architecture and operating system you are presently running. To compile to other platforms you must install other target platforms. This is done with the command:

`rustup target add <target>`

For example:

`rustup target add armv7-linux-androideabi`

<a href="https://rust-lang.github.io/rustup/cross-compilation.html" target="_blank">👉 &nbsp; The Rustup Book - Cross-compilation</a>

### &nbsp;

## How to manually cross-compile to a target of your choice

Sometimes, you might need to cross-compile your Rust code to a specific target separately from the Flutter build process. To ensure that everything runs smoothly, make sure you are in the _rust_ folder (on the same level as _Cargo.toml_).

### Manually cross-compile for macOS or iOS

On macOS and/or iOS you can start the process by executing this command in the Terminal:

`cargo build --target <target>`

For example:

`cargo build --target aarch64-apple-ios`

### Manually cross-compile for Android

On Android you can start the process by executing this command in the Terminal:

`cargo ndk -t <abi> build`

For example:

`cargo ndk -t arm64-v8a build`
