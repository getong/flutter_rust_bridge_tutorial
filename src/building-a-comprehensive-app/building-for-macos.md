# Building for macOS

---

Completing the remaining steps for macOS.

---

Everything is repeating. So in this section, I consolidate all the macOS instructions outlined in the previous chapters:

- [FRB Template App - modified Workflow -> macOS instructions](../building-without-iota/flutter-and-rust/frb-example-app/macos-instructions.md)
- [Building a Simple App -> Building for macOS](../building-a-simple-app/building-for-macos.md)

## macOS Steps

To integrate our Rust backend, we create an additional Xcode project first and add it as a "subproject" to the existing Flutter Xcode project (_macos/Runner.xcodeproj_). During the build, the inner Xcode project is built first, enabling its use by the parent project.

### Create the XCode project for our Rust library

{{#include ../building-a-simple-app/building-for-macos.md:11:36}}

    > A SCREENSHOT WILL FOLLOW! In the meanwhile refer to the Simple App video ("Building for macOS").

### Merge both projects

We need to incorporate the new Rust XCode project (_rust/rust.xcodeproj_) into our Flutter XCode project which was created inside the _macos_ folder when Flutter initialized our project.

{{#include ../building-without-iota/flutter-and-rust/frb-example-app/macos-instructions.md:27:27}}

<figure style="margin:0;">
<img src="/assets/macos_instructions/macos_1_playground.jpg" alt=""><figcaption style="font-size: 0.8em;text-align:center;"><p>Open <i>Runner.xcodeproj</i></p></figcaption>
</figure>

<figure style="margin:0;">
<img src="/assets/macos_instructions/macos_2.jpg" alt=""><figcaption style="font-size: 0.8em;text-align:center;"><p>Drag the file <i>rust.xcodeproj</i> into the Runner Project</p></figcaption>
</figure>

{{#include ../building-without-iota/flutter-and-rust/frb-example-app/macos-instructions.md:37:102}}

### Add the Security Entitlement

{{#include ../building-a-simple-app/building-for-macos.md:64:71}}

{{#include ../building-a-simple-app/building-for-macos.md:75:81}}

> THE VIDEO WILL FOLLOW...
